---
layout: post
title:  "Install OpenStack by Kolla Ansible"
date:   2018-11-28 14:20:33
typora-root-url: ../../../blog
---

 总文档 https://docs.openstack.org/kolla-ansible/latest/user/quickstart.html 

* fatal: [localhost]: FAILED! => {"changed": false, "failed": true, "msg": "Unknown error message: Tag 4.0.1 not found in repository docker.io/kolla/ubuntu-binary-kolla-toolbox"}.修改里面的openstack-version，从auto改为4.0.0。这是因为4.0.1没有push到docker hubs上面？
* deploy后，打开dashboard可以，但是static file都返回404。cleanup-containers & tools/cleanup-host under /usr/local/share/kolla-ansible/tools/, and try again. cleanup-host will remove networks. And then reboot.
* fatal: [localhost]: FAILED! => {"failed": true, "msg": "The conditional check ''{{ hostvars[item['item']]['ansible_' + hostvars[item['item']]['api_interface']]['ipv4']['address'] }}' not in '{{ item.stdout }}'' failed. The error was: Invalid conditional detected: EOL while scanning string literal (<unknown>, line 1)\n\nThe error appears to have been in '/usr/local/share/kolla-ansible/ansible/roles/rabbitmq/tasks/precheck.yml': line 54, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n\n- fail: msg=\"Hostname has to resolve to IP address of api_interface\"\n ^ here\n"} --- 我看安装程序已经设置hostname的ip为api_interface的ip。
* Docker will not be able to stop the nova_libvirt container with those running.----自己create的nova instance默认是用libvirt（因为我的host机器不是vm），shutdown后就可以运行cleanup-containers了。
* Cannot uninstall 'PyYAML'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall. — pip install pip==8.1.2

CentOS 7.3 多节点Kolla安装 http://www.chenshake.com/kolla-centos-over-more-than-7-3-node-installation/

/usr/share/kolla-ansible/init-runonce 文件里面 EXT_NET_CIDR这个是设置浮动IP的范围，必须设置好，这样 init-runonce 后，绑定 floating ip可以直接assign外网ip了，否则又得重新部署。http://www.chenshake.com/kolla-installation/ 这个里面有谈到。里面说 这个其实是从 neutron_external_interface 网卡访问的，如何确认？因为在 OpenWrt 是看不到的。`arp -an` 里面返回对应 IP 地址是 fa:16:3e:62:fb:ed，私营 mac，看了下也不是 vm nic mac，ui 里面看了下，是 network:router_gateway public1 的 Mac 地址。
这次忘了设置，修改再次运行时，报错 This tool should only be run once per deployment.
不想重新运行，按照这里 https://www.howtoing.com/openstack-networking-guide 在界面上创建网络。But notebook can’t ping floating ip & vm can’t ping 192.168.51.1. `yum install openswitch` then run ovs-vsctl show, otherwise command not found.
No idea. Re-deploy. tools/cleanup-containers & tools/cleanup-host as https://docs.openstack.org/kolla-ansible/latest/user/operating-kolla.html. cmd is in /usr/share/kolla-ansible/tools.

在 horizon 里面，外网或者内网网络是一个 IP 地址池（子网络），通过路由来连接两个网络，下面图中路由通过两个接口来连接两个网络。而浮动 ip 是外网子网里面的一个 ip，其和内网子网的某个 ip 做了绑定，这样就能通过浮动 ip 直接访问内网虚拟机。

Vm ssh 免密码登录必须要在 root 用户下才能进，密钥在root用户下？是的，安装用的是 root 账户。
部署好后，默认的就可以访问 horizon，在 network_interface 上面，admin password is in  /etc/kolla/passwords.yml:keystone_admin_password.

安装好后，很多服务比如swift没有安装。这个我得自己弄么？Just edit /etc/kolla/globals.yml and enable serivce like cinder. Then kolla-ansible deploy -i all-in-one again. Then OK.
今天又重新装了遍，安装好后，发现horizon没有装（现在默认不装了？），修改global.yaml，enable后重新deploy，居然也没有错，然后可以访问horizon。
可以额外添加的服务 https://github.com/openstack/kolla-ansible/blob/master/README.rst#openstack-services 注释里面的值就是默认值。比如默认打开了 horizon, heat。但是我看没有 heat 这个命令，仿照 openstackclient 安装方法 pip install python-heatclient，但是运行时报警：WARNING (shell) "heat stack-list" is deprecated, please use "openstack stack list” instead。

### magnum

既然已经有了：enable_horizon_magnum & enable_magnum，打开设置，kolla-ansible deploy。是很方便，就是似乎每次添加一个组件都挺耗时。
刷新后界面上已经有了新的菜单项 container Infra，但是会出错误消息：Error: Unable to retrieve the cluster templates. Error: Unable to retrieve the stats.
客户端用命令行安装 `pip install -U python-magnumclient`，这个是想当然猜出来的。`magnum cluster-list` 报错：
```
ERROR: 'NoneType' object has no attribute 'replace' (HTTP 500) (Request-ID: req-6c01fbcf-f883-41e3-a7f9-cecf92c7cf34)
```
https://stackoverflow.com/questions/52466203/error-nonetype-object-has-no-attribute-replace-http-500-openstack-magnum 这里同样问题，说是 github 已经 fix 了。但是我看 /etc/kolla/magnum-conductor 下面还是用的 www_authenticate_uri，什么情况。
重新部署，这次用开发模式，git clone kolla & kolla-ansible，git checkout stable/rocky。为什么要有两个项目？然后安装 pip install kolla/ & pip install kolla-ansible/， 否则没有命令可以用啊，这个文档里面没有写。其实命令都在 kolla-ansible/tools 代码下面。但是  /etc/kolla/magnum-conductor 这个目录是谁产生的？开发模式下没有看到这个目录。安装过程中？算了，重新安装 rocky 版本试试。这次使用 `kolla-ansible -i ./all-in-one deploy -t magnum` 就可以安装这个模块。`magnum cluster-list` 返回：

```
ERROR: Unable to establish connection to http://192.168.51.254:9511/v1/clusters: HTTPConnectionPool(host='192.168.51.254', port=9511): Max retries exceeded with url: /v1/clusters (Caused by NewConnectionError('<urllib3.connection.HTTPConnection object at 0x7fe0abfd3d10>: Failed to establish a new connection: [Errno 111] Connection refused',))
```
但是我看 `magnum docker` 已经运行起来了。好吧，还是老老实实运行 `kolla-ansible -i ./all-in-one deploy`，然后错误还是回到了 NoneType。
直接跑到 /usr/share/kolla-ansible/ansible/roles/magnum 下修改 magnum.conf.j2，原来上面的/etc/kolla 里面都是根据这个来产生的，重新 deploy 后可以看到/etc/kolla 下面被修改了，但是 docker ps 显示对应镜像还是 半小时前的，所以错误还是一样，如何重新生成呢？算了，清空重新部署。现在magnum ui & cli 都可以运行不出错。这个重新部署很要命，有的可以，有的要清空然后重新部署。有地方说重启全部容器就可以。
然后在界面创建 cluster template，这比命令行方便。但是出现错误（错误都只在 http response 里面才能看到）：`Cluster type (vm, None, kubernetes) not supported (HTTP 400)`
这个错误在命令行下可以看到，所以 UI 做的不行还不如命令行。这种 hello world 一定要能测试通过，否则就失去了 UI 快速上手的意义。
上面错误在 https://ask.openstack.org/en/question/116089/cant-create-k8s-cluster-in-magnum-image-issue/ 解释很清楚，原因在于我的 os image 必须带一个属性 **os_distro** 标明其 linux 发行名，属性可以在 ui 里面的 image meta 加（失败，只能命令行，这个 UI 似乎很烂啊！）。但是现在只支持 fedora-atomic, coreos，看来这个 magnum 知道 k8s 配置对操作系统很敏感。
集群创建中一直报错：

```
Service cinder is not available for resource type Magnum::Optional::Cinder::Volume, reason: cinder volumev3 endpoint is not in service catalog.
```
这个 cinder 是必须的么？开启 enable_cinder，重新 deploy，这个不需要任何 backend？先这样再说。
创建集群还是出错，这次用 cli 查看，因为 ui 又连不上。
```
magnum cluster-show fantest
Resource CREATE failed: resources[0]: resources.kube_masters.Property error: resources.docker_volume.properties.volume_type: Error validating value '': The VolumeType () could not be found.
```
修改模板 `magnum cluster-template-update fedora add volume_driver=cinder`，还是一样。我在界面创建卷的时候也看到 No Volume type。`openstack volume type list` 也为空。
好吧，enable_ceph，重新 deploy，没看到啥变化，只是多了两个 ceph 容器。手工创建一个 volume type，然后创建一个该 type 的 volume，失败。看来部署 Cehp 没有那么简单。

下面 Ceph 配置好后，回到这里。现在可以在界面创建一个 volume，但是创建 k8s 集群时依然同样错误。[这里](https://ask.openstack.org/en/question/110729/magnum-cluster-create-k8s-cluster-error-resourcefailure/)说缺少一个`default_docker_volume_type` 字段，`docker exec -it magnum_conductor` 进去后直接修改，然后 restart container，后来发现 restart 后值丢失，原来要修改 `/etc/kolla/magnum-*` 下面的对应文件，我猜容器是用 mount 目录的方式来访问配置，这种操作如果放在 k8s 下面做就简单方便很多。这个值原始定义在 `/usr/share/kolla-ansible/ansible/roles/magnum/defaults/main.yml` 中。

现在开始漫长的创建 k8s 集群了。

### Ceph

感觉相关依赖没有做好，后面加 ceph，前面创建好的 cinder 容器没有重建，容器里面的配置都没有修改，这怎么能行呢？清除 kolla 然后重建集群。登录到后发现 cinder-api 下面还是没有 /etc/ceph/ceph.conf 文件，后来发现 cinder-volume 下面有，但是 ceph status 无法登录。ceph-mgr 容器运行 ceph osd pool ls 返回四个已经创建好的 pool：images, volumes, backups, vms. 但是 ceph -s 返回 0 kB used, 0 kB / 0 kB avail。日。https://docs.openstack.org/kolla-ansible/latest/reference/storage/ceph-guide.html 这里有详细配置，原来这个需要给硬盘加标签，然后 kolla 会把这个硬盘分配给 ceph。我只运行：
​    parted /dev/sdb -s -- mklabel gpt mkpart KOLLA_CEPH_OSD_BOOTSTRAP 1 -1
这个命令，其他的都不懂干啥用，还说可能至少要 3 个节点，我 all-in-one 怎么弄？不管它。这个地方文档搞的有些复杂，还是 ceph 本来就难配置？
还是不行，`cinder service-list` 显示 cinder-volume  ms1@rbd-1 是 down 的状态。但是我看 kolla/centos-source-cinder-volume:rocky 这个容器已经起来啊，这个 ms1 是宿主机 hostname，后面 @rbd-1是啥？现在问题是几种方法都没有在 docker ps 中看到 ceph-osd/ceph-rbd 之类的容器。
再次细看文档：all-in-one 情况下，需要设置 osd pool default size = 1，但是没有 /etc/kolla/config/ceph.conf 这个文件，修改 /usr/share/kolla-ansible/ansible/roles/ceph/templates/ceph.conf.j2，重新 deploy 后已经能看到 /etc/kolla/ceph-osd/ceph.conf 里面有我加的配置。但是看上去还是不行。容量还是为 0 ，/dev/sdb 似乎根本没有考虑进去。
换成 Queens 版本，因为这个没有 Bluestore，也不知道是不是这个原因。再不行得看 ansible 代码了。
http://docs.ceph.com/docs/master/start/quick-ceph-deploy/ 这里创建 rbd 都是直接命令行，没有放到配置里面。
不行，/usr/share/kolla-ansible/ansible/roles/ceph/tasks/start_osds.yml 创建 osd 的脚本，但是如何知道运行结果呢？kolla-ansible 运行只输出到屏幕，没有地方看全部日志，可能我没找到。启用 verbose，使用命令 
   kolla-ansible ...  - v | tee log。
果然发现：

```
TASK [ceph : Looking up disks to bootstrap for Ceph OSDs]  *********
ok: [localhost] => {"changed": false, "cmd": ["docker", "exec", "-t", "kolla_toolbox", "sudo", "-E", "ansible", "localhost", "-m", "find_disks", "-a", "partition_name=KOLLA_CEPH_OSD_BOOTSTRAP_BS match_mode='prefix' use_udev=True"], "delta": "0:00:01.454122", "end": "2018-11-27 21:46:51.368490", "failed_when_result": false, "rc": 0, "start": "2018-11-27 21:46:49.914368", "stderr": "", "stderr_lines": [], "stdout": "localhost | SUCCESS => {\r\n    \"changed\": false, \r\n    \"disks\": \"[]\"\r\n}", "stdout_lines": ["localhost | SUCCESS => {", "    \"changed\": false, ", "    \"disks\": \"[]\"", "}"]}
```
返回空数组，这是没有找到合适的磁盘了？**KOLLA_CEPH_OSD_BOOTSTRAP_BS** 为什么是这个？这个是 bluestore，应该是 KOLLA_CEPH_OSD_BOOTSTRAP。这个分区名字是在 /usr/share/kolla-ansible/ansible/roles/ceph/defaults/main.yml 中决定的，接着由 ceph_osd_store_type 决定，在 /usr/share/kolla-ansible/ansible/group_vars/all.yml 这个默认是 bluestore。好的，我在 /etc/kolla/globals.yml 加上 ceph_osd_store_type: “filestore"。重新发布，现在发现错误了，手工在 kolla-toolbox container 里面运行：
```
ansible localhost -m find_disks -a "partition_name=KOLLA_CEPH_OSD_BOOTSTRAP match_mode='prefix' use_udev=True”
```
返回错误：
```
localhost | FAILED! => {
    "changed": false, 
    "failed": true, 
    "msg": "UnicodeDecodeError('ascii', '\\xe6\\x96\\xb0\\xe5\\x8a\\xa0\\xe5\\x8d\\xb7', 0, 1, 'ordinal not in range(128)')"
}
```
有对应磁盘反而报错，奇怪。下载 find_disk.py，本地运行，发现磁盘有个 LABEL 『新加卷』，导致出错。parted /dev/sdb 这个命令默认就会产生这个 label。折腾各种命令来修改 label，最后发现这个『新加卷』是原来的 Windows 磁盘，parted 并不会删除旧有分区。mkfs.ext4 格式化之。现在 OK 了！
检查最后成功状态：

```
[root@ms1 fan]# lsblk /dev/sdb
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sdb      8:16   0 238.5G  0 disk 
├─sdb1   8:17   0 233.5G  0 part /var/lib/ceph/osd/6fce7aad-0b42-4a0e-bdca-8bb7c9ed7135
└─sdb2   8:18   0     5G  0 part 
```
sdb1 下面都是散放的文件，这个就是 filestore 的意思？
现在可以看到有个 kolla/centos-source-ceph-osd:queens 容器在运行。

[Kolla集成外接ceph存储](https://blog.csdn.net/dylloveyou/article/details/79114741)  集成到我原来创建好的 rook ceph 上去？滑稽。

现在已经用 `openstack coe` 来代替 magnum 命令了。

init-runonce 会创建很多初始化的资源，比如网络路由、cirros 磁盘镜像、虚机类型，但是只能运行一次。

### 虚拟机一直是 scheduling 状态

https://docs.openstack.org/kolla-ansible/latest/user/troubleshooting.html 这里有调试方法，用 docker exec -it 进入 shell，cat /var/log/kolla/nova/nova-scheduler.log，
```
AMQP server on 192.168.51.247:5672 is unreachable: [Errno 111] ECONNREFUSED. Trying again in 6 seconds. Client port: None: error: [Errno 111] ECONNREFUSED
```
界面显示服务 nova-scheduler down 状态。nova service-list 也显示为 down。
https://ask.openstack.org/en/question/54526/amqp-server-on-1000115672-is-unreachable/ 同样问题
docker ps 返回里面有  kolla/centos-binary-rabbitmq:queens 这个 docker 运作中，shell 进去，rabbitmqctl status，lsof -i :5672 看到已经有很多连接了。不对，上面unreachable日志是昨天的。docker ps -a 返回所有 docker，发现 kolla/centos-binary-nova-scheduler:queens 已经退出了。
```
    b63907bcea6e        kolla/centos-binary-nova-scheduler:queens              "kolla_start"       16 hours ago        Exited (0) 13 hours ago                       nova_scheduler
```
为什么是 13 小时前，几天上午我开机的时候没有尝试重启这个 docker？我自己运行 docker start nova_scheduler (不能 start id)，可以起来，然后就一切好了。(我有尝试关掉防火墙和 selinux，应该没有关系)，有的说这个和 NTP 和启动依赖有关系，这个依赖 mq。

Docker ps 有返回 kolla/centos-binary-horizon:queens，这里才知道安装的是 queens 版本。命令行用 —version 返回的都是数字。

`ps aux` 里面有返回 qemu-kvm 进程，为什么公司机器的就没有呢？难到是因为我用的 qcow2 格式，可以该换 raw 格式，可能性不大。

还有虚机运行的磁盘文件是直接放在宿主机上面：-drive file=/var/lib/nova/instances/78687529-9333-429f-a184-9a13c725fcca/disk,format=qcow2，如果使用了 ceph，会放到 ceph 上面？

### Think
*  部署是简单了，但是PASS服务再怎么加？或者有个kubenetes那种快速部署应用也可以啊。
* Docker 用的不错。那用了 docker 还用 openstack vm 干啥呢？技术变化太快，总的来说：OpenStack plays the role of the overall data center management. KVM as the multi-tenant compute resource management, and Docker containers as the application deployment package.
* 用 Docker，出了错只能直接监控，显然用 k8s 更好些，但牵涉到网络、存储这个问题就更复杂了。

