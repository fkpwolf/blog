---
layout: post
title:  "GlusterFS"
date:   2018-9-11 14:20:33
typora-root-url: ../../../blog
---

### 安装

今天想到如果 16GB 内存机器安装这个，会不会比较省资源。 这个比 Ceph 轻，安装也容易，但只支持块设备，对象和文件存储都不支持。好处在 k8s storage 支持范围内。

<https://docs.gluster.org/en/latest/Install-Guide/Overview/>
```
gluster volume info
Connection failed. Please check if gluster daemon is operational. 
systemctl start&enable glusterd.service 
[glusterd.c:1324:glusterd_init_var_run_dirs] 0-management: Unable to create directory /run/gluster/snaps ,errno = 13 [Permission denied] 
==>setenforce 0 
gluster volume create gv0 gluster-1:/export/sdb1/brick 
```

<https://github.com/heketi/heketi> RESTful based volume management framework for GlusterFS，独立运行，本身有数据库维护集群的拓扑结构，有点像 ceph monitor，但是只是控制面板，数据面板是直接连各个 GlusterFS 节点，k8s glusterfs plugin 通过这个来和 GlusterFS 交互。

<https://www.cnblogs.com/breezey/p/8849466.html> 这个中文的指导不错，总的思路是 heketi 通过 ssh 证书来添加各个运行中的 glusterfs 节点。

安装方式有多种，可以安装在 k8s 内部，但是看了下还挺麻烦，我还是单独起个进程吧。 创建卷：
```
failed to create volume: failed to create volume: Failed to allocate new volume: No space 
./heketi-cli --server http://vm1:8080/ volume create --size=1 
Error: Failed to allocate new volume: No space 
./heketi-cli --server http://vm1:8080/ volume create --size=1 --durability=none 
```
这种可以，假假的。
```
fandeMac:bin fan$ ./heketi-cli --server http://192.168.1.121:8080/ volume delete 83ebfa75567b8b2138dd7df53a53c947 
Error: Unable to get snapshot information from volume vol_83ebfa75567b8b2138dd7df53a53c947: ssh: handshake failed: ssh: unable to authenticate, attempted methods [none publickey], no supported methods remain 
```
这个命令报错很详细，ssh 有点搞。现在我把 heketi 安装在其他节点上，然后 `ssh-copy-id -i heketi_key.pub new_node` 来拷贝证书到新节点上，这样 heketi 就可以用证书免密登录所有 glusterfs 节点。 
```
fandeMac:bin fan$ ./heketi-cli --server http://vm1:8080/ device add --name="/dev/vdb1" --node "5ef3f8c98a2e7456db1a05f7c60088e1" 
Error: WARNING: xfs signature detected on /dev/vdb1 at offset 0. Wipe it? [y/n]: [n] 
  Aborted wiping of xfs. 
  1 existing signature left on the device. 
```
要对/dev/vdb 重新分区，统一纳入 heketi 管理 

    ./heketi-cli --server http://vm1:8080/ topology info 

现在可以显示真实的大小了。--durability=none 创建的 volume 也可以删除了。 

### Kubernetes

因为默认 replica 是开启的，只能使用 Distribute volume: volumetype: none 这种模式了，StorageClass 也支持这个。 

现在 PV 可以创建了，但是 Pod 报错：mount: unknown filesystem type ‘glusterfs’exe ，解决办法：节点上安装  `apt install glusterfs-client |  yum install glusterfs-fuse` 。现在可以正常 mount 了，一切正常。 

为什么 GlusterFS 就可以这样，ceph 却要一个单独的 pod？可能是因为 gluster 只要 mount 这个常见命令就够了（mount 则需要一个文件系统的插件），这个在容器中就可以调用？而 ceph 则需要 rbd 等专属命令，这个即使安装在节点上容器中也无法调用？有个 [feature](https://github.com/kubernetes/features/issues/278) Kubernetes can run mount utilities in pods instead on the host，这样节点上就不用安装任何额外的包了。但是为什么 Ceph 连安装额外的包后都不能工作？真是辣鸡啊。 另外一个原因是 ceph 文件系统 mount 是在内核层，而 glusterfs 是**用户层**。

但是在使用 volume 的 k8s 节点上看不到相关信息（dmesg, lsblk, lsmod, df -h…），后来发现： 
```
ubuntu@vm1:~$ mount | grep gluster 
192.168.1.121:vol_a2f74b885204c095191dbac9e8c8bd51 on /var/lib/kubelet/pods/7d812851-b62e-11e8-ae0a-428a7547569e/volumes/kubernetes.io~glusterfs/pvc-7cefa502-b62e-11e8-ae0a-428a7547569e type fuse.glusterfs (rw,relatime,user_id=0,group_id=0,default_permissions,allow_other,max_read=131072) 
```
因为我用的是 Distribute volume，所以这里只有一个节点。这个是在用户空间 mount，没有内核参与，会比 Ceph 稳定点么？这种 mount 方式和 NFS 或者 Samba 有点像。 

最后的 [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/#glusterfs) 定义： 
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gluster
provisioner: kubernetes.io/glusterfs
parameters:
  resturl: "http://192.168.51.11:8080"
  volumetype: none
```
Distribute volume: volumetype: none，表示没有副本，因为我只有一个 glusterfs 节点。 没有开启认证，所以一切都很简单，不需要 secret，而且 heketi 默认是关闭认证的。 

glusterfs provider 也会在 k8s 中创建一个 `gluster-dynamic-<claimname>` 服务，这个服务直接绑定全部的 glusterfs 节点，这个负载均衡很有用，但是给谁用呢？一般 Pod 都是直接 mount pv。如何用？使用 fuse.glusterfs 直接 mount？ 

Heketi 能管理多个cluster，多个node，并非只是简单的 REST API 提供，而是一个有自己数据库的可以管理卷的分配的服务。这样 k8s 才可以动态分配 Volume。如果有多个 cluster，而 StorageClass 没有指定，则 heketi 会遍历多个 cluster 找到合适的地方。 
```
./heketi-cli --server http://vm1:8080 topology info
./heketi-cli --server http://vm1:8080 volume list
./heketi-cli --server http://vm1:8080 volume delete foo
./heketi-cli --server http://vm1:8080 volume create --size=1
```
heketi 这么多接口，如果能提供 web ui 就方便多了，不过 heketi 上面没人相应，只有这个 <https://github.com/orachide/heketi-ui> 。heketi 也不提供监控信息吧，这个应该属于商业产品范围了。

常驻运行可以用`nohup ./heketi --config=./heketi.json &`，如果要转换为 systemd 服务，可以参考这里 <https://github.com/heketi/heketi/blob/master/extras/systemd/heketi.service>。

<https://www.redhat.com/zh/technologies/storage/gluster> 这个原来是红帽的技术。这两个产品在 centos/fedora 上面都可以直接安装，适配更好。 

**总的来说**，比 ceph 简单多了：server 配置简单，k8s 里面也简单，申请删除 pv 都很快，也不需要先搞 secret。heketi 也为 k8s 做了适配。而 ceph 现在觉得更多是在 openstack 生态圈里面。 

Heketi 居然在 centos 里面没包安装，fedora 有，ubuntu 没有。简单方法 `sudo nohup ./heketi --config=./heketi.json > log &`。感觉 fedora 才是好，centos 7 kernel 有点老，cloud image 也大多了。 

今天发现 Mongodb 起不来，日志有： 

    Error executing 'postInstallation': Group '2001' not found 

看了半天，发现是 GlusterFS 的问题。<https://github.com/helm/monocular/issues/419> <https://github.com/helm/charts/issues/2488> 新的 mongodb-4.0.6 chart 已经修复。 [原因在这里](https://github.com/bitnami/bitnami-docker-drupal/issues/88)，好像是文件系统权限的问题。

### Inside

具体存储类型分类 <https://docs.gluster.org/en/v3/Quick-Start-Guide/Architecture/>  Distributed, Replicated, Distributed Replicated, Striped。 图文并茂，写的不错。

和 ceph 一样的在于对于磁盘格式用的都是 xfs，总磁盘分区拓扑是 LVM，下面 LV 分区是各个 xfs 格式分区。LVM 是管理多个磁盘的工具。 

基于 GlusterFS 实现 Docker 集群的分布式存储 <https://www.ibm.com/developerworks/cn/opensource/os-cn-glusterfs-docker-volume/index.html> "GlusterFS 借助 TCP/IP 或 InfiniBand RDMA 网络将物理分布的存储资源聚集在一起，使用单一全局命名空间来管理数据。GlusterFS 基于可堆叠的用户空间设计，可为各种不同的数据负载提供优异的性能。" 

但是也要多个机器，这不是现有鸡还是蛋的问题了。ovirt 也支持这个，毕竟都是红帽一家的。

『GlusterFS 架构中没有元数据服务器组件，这是其最大的设计这点，对于提升整个系统的性能、可靠性和稳定性都有着决定性的意义。』和 Ceph 类似，但是 Ceph 有 monitor，这个如何管理数据的分布？根据卷的类型来？比如如果是 Replica volume 类型的，肯定是每个节点都有了。 但是 heketi 呢？这个不是单点故障么？如果 heketi 挂了，volume 级别的 meta 信息应该还在，这本身就由 glusterfs 节点维护，cluster/node 级别的可能就不存在了。

如果一个卷分布在多个磁盘上(replica >2)，samba 客户端读取只能设置一个 Ip 吧，如何提高性能？ 

Volume 里面有属性： 
```
heketi-cli topology info  
Mount: 192.168.51.130:vol_573e4b4f72cecb867cbddb75260dab34 
Mount Options: backup-volfile-servers=192.168.51.187 
```
但是 k8s mount node 上面没有使用这个 backup server： 
```
[centos@k8s-2-new ~]$ mount | grep gluster 
192.168.51.130:vol_573e4b4f72cecb867cbddb75260dab34 on /var/lib/kubelet/pods/7f062c23-f906-11e8-ac1b-3eede03e70f1/volumes/kubernetes.io~glusterfs/pvc-7ed0a1a3-f906-11e8-ac1b-3eede03e70f1 type fuse.glusterfs (rw,relatime,user_id=0,group_id=0,default_permissions,allow_other,max_read=131072) 
```
如何触发来测试这个副本保护机制？[Setting Up GlusterFS Client](https://docs.gluster.org/en/latest/Administrator%20Guide/Setting%20Up%20Clients/)，有个参数 backupvolfile-server，当主 server 挂掉，会连接第二个，和性能没关系，HA 的。 k8s glusterfs provider bug? 

如果在节点上运行 `ps aux|grep gluster`，会发现对于每个 PVC（对 gluster 是一个 volume） 都运行了一个 glusterfsd 进程，如果在 k8s 下面，可以做成一个 pod。

### 容量翻倍的问题

一个 PV，在 gluserfs server 上面有两个磁盘 vg_tmeta & vg_tdata，导致占用容量翻了倍。gluster volume info 运行看 Type: Distribute。heketi-cli topology info： 
```
Devices:
        Id:af07f63706290336a9e68f68ee8447ba   Name:/dev/vdb1           State:online    Size (GiB):89      Used (GiB):84      Free (GiB):5       
            Bricks:
                Id:03a421ebc0f7a68d138d9fa8afa67739   Size (GiB):8       Path: /var/lib/heketi/mounts/vg_af07f63706290336a9e68f68ee8447ba/brick_03a421ebc0f7a68d138d9fa8afa67739/brick
                Id:5f2a4e8ac83577fe65a53265aa9ee892   Size (GiB):8       Path: /var/lib/heketi/mounts/vg_af07f63706290336a9e68f68ee8447ba/brick_5f2a4e8ac83577fe65a53265aa9ee892/brick
                Id:6fa247bbe01d2e2656b33eade73e8131   Size (GiB):8       Path: /var/lib/heketi/mounts/vg_af07f63706290336a9e68f68ee8447ba/brick_6fa247bbe01d2e2656b33ee73e8131/brick
```
上面显示有 3 个卷，但是 Used 显示已经用了 84G， 但是 `lsblk -f` 却有 6 个磁盘，vg_tmeta & vg_tdata： 
```
[fedora@gluster-1 ~]$ sudo vgdisplay
  --- Volume group ---
  VG Name               vg_af07f63706290336a9e68f68ee8447ba
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  162
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                6
  Open LV               3
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               89.87 GiB
  PE Size               4.00 MiB
  Total PE              23007
  Alloc PE / Size       6188 / 24.17 GiB
  Free  PE / Size       16819 / <65.70 GiB
  VG UUID               9WZzBd-CJlr-MWWD-3qRO-z4gD-e0Za-dysaKt
```
这个显示是正确的，用了 24 GB。lvdisplay 显示有 6 个逻辑卷。再次创建一个有状态 helm chart，`pvc pending，Failed to allocate new volume: No space`。 升级 heketi 到 9.0 版本，删除所有 PV，topology info 也显示已经删除，但是` Size (GiB):89 Used (GiB):60  Free (GiB):29`，这时为何？原来要运行 `heketi-cli device resync <id_device>`，这样磁盘大小就同步了。 然后再次安装回有状态 chart，gluster vm 还是显示两个：tmeta & tdata，但是 `heketi-cli topology info` 显示已经是正常了。不知道是版本问题还是需要运行 resync 命令。 

### volume 无法删除的问题
先是在 k8s 中无法删除，`heketi-cli volume delete id` 显示 error，但是没有有用信息。后来发现一个节点有 mount fuse.glusterfs，umount 之，但并没有用。先提了 [issue](https://github.com/heketi/heketi/issues/1538)。现在发现发送 delete 命令时服务器端日志都显示：

    [negroni] Started GET /volumes/eab0e046b9cdb9a07572d18549be64f2
    [negroni] Completed 200 OK in 7.151436ms

使用 https://putsreq.com 来查看命令发送的 REST 请求是否是 DELETE，返回`Error: server did not provide a message (status 404: Not Found)`，应该是 URL 有加参数的缘故：`req, err := http.NewRequest("DELETE", c.host+"/volumes/"+id, nil)`，而 pustreq 和 httpbin 都不支持 wildcard matching，可能是安全考虑，后来发现 Postman 本身带有这个功能，检查后发现 heketi-cli 确实发送的 DEL 命令。
手工用`gluster volume delete id`命令删除卷，成功。但是 heketi 这边还是显示原来的卷，heketi db 只是单向的数据流？k8s 内部的 PV 可以自动删除。祭出命令`tcpdump port 8080 -nA`，对比两者请求，发现问题在`./heketi-cli --server http://192.168.51.187:8080/ volume list` 的请求为 `DEL //volume/id`，这时会返回 `301 Moved Permanently` 并附上正确的 URL `/volume/id`，cli 拿到新的 URL 后统一重发 GET 请求。DELETE is not a safe method，所以 301 跳转时会转换为 GET，这个是 golang http 的[行为](https://github.com/golang/go/issues/13994)。这也是为什么 GET 命令可以成功的原因。

调试发现其请求过程挺有趣，比如创建一个 volume：
```
-> POST /volumes
<- 202 Accepted. Location: /queue/qid
-> GET /queue/qid
<- 200 OK
...keep polling
-> GET /queue/id
<- 303 See Other. Location: /volumes/vid
-> GET /volumes/vid
<- 200 OK JSON {...}
```
### Odroid HC1

Ubuntu 18.04.1 LTS，两台机器，odroid-1 & odroid-2，各带一机械硬盘，各千兆网线接交换机，heketi 安装在 odroid-1。

`apt install glusterfs-server lvm2 thin-provisioning-tools` 

如果以前安装，先 vgdisplay/vgremove清除已有 lvm 分区，否则 add device 时候说磁盘没有初始化。 

    heketi-cli --server "http://192.168.51.187:8080" node add --management-host-name 192.168.51.130 --storage-host-name 192.168.51.130 --zone 1 --cluster foo 

这里全部用 IP，否则各种问题。 

存储(nas)是这个板子最好用途了，装 k8s 集群作为计算节点的话内存太小。 

我设置了磁盘休眠，这个对系统是否有影响？ 

如何测试个性能？