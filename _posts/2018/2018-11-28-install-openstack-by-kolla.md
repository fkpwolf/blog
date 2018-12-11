---
layout: post
title:  "Install OpenStack by Kolla Ansible and Get LoadBalancer IP"
date:   2018-11-28 14:20:33
typora-root-url: ../../../blog
categories:
  - cloud
  - setup
---

### 安装

指导文档：https://docs.openstack.org/kolla-ansible/latest/user/quickstart.html 

CentOS 7.3 多节点Kolla安装 http://www.chenshake.com/kolla-centos-over-more-than-7-3-node-installation/

* fatal: [localhost]: FAILED! => {"changed": false, "failed": true, "msg": "Unknown error message: Tag 4.0.1 not found in repository docker.io/kolla/ubuntu-binary-kolla-toolbox"}. → 修改里面的openstack-version，从auto改为4.0.0。这是因为4.0.1没有push到docker hubs上面？
* deploy后，打开dashboard可以，但是static file都返回404。→ cleanup-containers & cleanup-host in /usr/local/share/kolla-ansible/tools. 这是清空重新部署的常用命令。
* `fatal: [localhost]: FAILED! => {"failed": true, "msg": "The conditional check ''{{ hostvars[item['item']]['ansible_' + hostvars[item['item']]['api_interface']]['ipv4']['address'] }}' not in '{{ item.stdout }}'' failed. The error was: Invalid conditional detected: EOL while scanning string literal (<unknown>, line 1)\n\nThe error appears to have been in '/usr/local/share/kolla-ansible/ansible/roles/rabbitmq/tasks/precheck.yml': line 54, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n\n- fail: msg=\"Hostname has to resolve to IP address of api_interface\"\n ^ here\n"}` → 我看安装程序已经设置hostname的ip为api_interface的ip。
* Docker will not be able to stop the nova_libvirt container with those running. → 自己create的nova instance默认是用libvirt（因为我的host机器不是vm），shutdown后就可以运行cleanup-containers了。
* Cannot uninstall 'PyYAML'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall. → pip install pip==8.1.2
* /usr/share/kolla-ansible/init-runonce 文件里面 EXT_NET_CIDR这个是设置浮动IP的范围，必须设置好，这样 init-runonce 后，绑定 floating ip可以直接assign外网ip了，否则又得重新部署。http://www.chenshake.com/kolla-installation/ 这个里面有谈到。里面说 这个其实是从 neutron_external_interface 网卡访问的，如何确认？因为在 OpenWrt 是看不到的。`arp -an` 里面返回对应 IP 地址是 fa:16:3e:62:fb:ed，私营 mac，看了下也不是 vm nic mac，ui 里面看了下，是 network:router_gateway public1 的 Mac 地址。
这次忘了设置，修改再次运行时，报错 This tool should only be run once per deployment.
不想重新运行，按照这里 https://www.howtoing.com/openstack-networking-guide 在界面上创建网络。But notebook can’t ping floating ip & vm can’t ping 192.168.51.1. `yum install openswitch` then run ovs-vsctl show, otherwise command not found.
No idea. Re-deploy. tools/cleanup-containers & tools/cleanup-host as https://docs.openstack.org/kolla-ansible/latest/user/operating-kolla.html. cmd is in /usr/share/kolla-ansible/tools.

Vm ssh 免密码登录必须要在 root 用户下才能进，密钥在root用户下？是的，安装用的是 root 账户。
部署好后，默认的就可以访问 horizon，在 network_interface 上面，admin password is in  /etc/kolla/passwords.yml:keystone_admin_password.

安装好后，很多服务比如swift没有安装。这个我得自己弄么？Just edit /etc/kolla/globals.yml and enable serivce like cinder. Then kolla-ansible deploy -i all-in-one again. Then OK.
今天又重新装了遍，安装好后，发现horizon没有装（现在默认不装了？），修改global.yaml，enable后重新deploy，居然也没有错，然后可以访问horizon。
可以额外添加的服务 https://github.com/openstack/kolla-ansible/blob/master/README.rst#openstack-services 注释里面的值就是默认值。比如默认打开了 horizon, heat。但是我看没有 heat 这个命令，仿照 openstackclient 安装方法 pip install python-heatclient，但是运行时报警：WARNING (shell) "heat stack-list" is deprecated, please use "openstack stack list” instead。

init-runonce 会创建很多初始化的资源，比如网络路由、cirros 磁盘镜像、虚机类型，但是只能运行一次。

### magnum

既然已经有了：enable_horizon_magnum & enable_magnum，启用，然后 kolla-ansible deploy，是很方便，就是似乎每次添加一个组件都挺耗时。
刷新后界面上已经有了新的菜单项 container Infra，但是会出错误消息：Error: Unable to retrieve the cluster templates. Error: Unable to retrieve the stats.
客户端用命令行安装 `pip install -U python-magnumclient`，这个是想当然猜出来的（现在已经用 `openstack coe` 来代替 magnum 命令了。）。`magnum cluster-list` 报错：
```
ERROR: 'NoneType' object has no attribute 'replace' (HTTP 500) (Request-ID: req-6c01fbcf-f883-41e3-a7f9-cecf92c7cf34)
```
[这里](https://stackoverflow.com/questions/52466203/error-nonetype-object-has-no-attribute-replace-http-500-openstack-magnum) 同样问题，说是 GitHub 已经 fix 了。但是我看 /etc/kolla/magnum-conductor 下面还是用的 www_authenticate_uri，什么情况。
重新部署，这次用开发模式，git clone kolla & kolla-ansible，git checkout stable/rocky。为什么要有两个项目？然后安装 pip install kolla/ & pip install kolla-ansible/， 否则没有命令可以用啊，这个文档里面没有写。其实命令都在 kolla-ansible/tools 代码下面。但是  /etc/kolla/magnum-conductor 这个目录是谁产生的？开发模式下没有看到这个目录。安装过程中？算了，重新安装 rocky 版本试试。这次使用 `kolla-ansible -i ./all-in-one deploy -t magnum` 就可以安装这个模块。`magnum cluster-list` 返回：
```
ERROR: Unable to establish connection to http://192.168.51.254:9511/v1/clusters: HTTPConnectionPool(host='192.168.51.254', port=9511): Max retries exceeded with url: /v1/clusters (Caused by NewConnectionError('<urllib3.connection.HTTPConnection object at 0x7fe0abfd3d10>: Failed to establish a new connection: [Errno 111] Connection refused',))
```
但是我看 `magnum docker` 已经运行起来了。好吧，还是老老实实运行 `kolla-ansible -i ./all-in-one deploy`，然后错误还是回到了 NoneType。

直接跑到 /usr/share/kolla-ansible/ansible/roles/magnum/templates 下修改 magnum.conf.j2，原来上面的 /etc/kolla 都是根据这个来产生的，重新 deploy 后可以看到/etc/kolla 下面被修改了，但是 docker ps 显示对应镜像还是 半小时前的，所以错误还是一样，如何重新生成呢？清空重新部署。现在magnum ui & cli 都可以运行不出错。这个重新部署很要命，有的可以，有的要清空然后重新部署，有地方说重启全部容器就可以。

然后在界面创建 cluster template，这比命令行方便。但是出现错误（错误都只在 http response 里面才能看到）：`Cluster type (vm, None, kubernetes) not supported (HTTP 400)`
这个错误在命令行下可以看到，所以 UI 做的不行还不如命令行。这种 hello world 一定要能测试通过，否则就失去了 UI 快速上手的意义。
上面错误在[这里](https://ask.openstack.org/en/question/116089/cant-create-k8s-cluster-in-magnum-image-issue/) 解释很清楚，原因在于我的 os image 必须带一个属性 **os_distro** 标明其 linux 发行名，属性可以在 ui 里面的 image meta 加（rocky 只能命令行，UI 会报错）。现在[只支持](https://docs.openstack.org/magnum/latest/user/#kubernetes) fedora-atomic, coreos，看来 magnum 知道 k8s 配置对操作系统很敏感。

    openstack image set --property os_distro=fedora-atomic foo

集群创建中一直报错：
```
Service cinder is not available for resource type Magnum::Optional::Cinder::Volume, reason: cinder volumev3 endpoint is not in service catalog.
```
这个 Cinder 是必须的么？开启 enable_cinder，重新 deploy，这个不需要任何 backend？先这样再说。
创建集群还是出错，这次用 cli 查看，因为 ui 又连不上。
```
magnum cluster-show fantest
Resource CREATE failed: resources[0]: resources.kube_masters.Property error: resources.docker_volume.properties.volume_type: Error validating value '': The VolumeType () could not be found.
```
修改模板 `magnum cluster-template-update fedora add volume_driver=cinder`，还是一样。我在界面创建卷的时候也看到 No Volume type。`openstack volume type list` 也为空。
好吧，enable_ceph，重新 deploy，没看到啥变化，只是多了两个 ceph 容器。手工创建一个 volume type，然后创建一个该 type 的 volume，失败。看来部署 Cehp 没有那么简单。

下面 Ceph 配置好后，回到这里。现在可以在界面成功创建一个 volume，但是创建 k8s 集群时依然同样错误。[这里](https://ask.openstack.org/en/question/110729/magnum-cluster-create-k8s-cluster-error-resourcefailure/)说缺少一个`default_docker_volume_type` 字段，`docker exec -it magnum_conductor` 进去后直接修改，然后 restart container，后来发现 restart 后值丢失，原来要修改 `/etc/kolla/magnum-*` 下面的对应文件，我猜容器是用 mount /etc 目录的方式来访问配置，这种操作如果放在 k8s 下面做就简单方便很多。这个值原始定义在 `/usr/share/kolla-ansible/ansible/roles/magnum/defaults/main.yml` 中。这个 volume type 没有绑定特定的 volume backend，可能被当做为默认类型。

现在开始漫长的创建 k8s 集群了。然后居然就可以了，一个 master，两个 minion，没有出现任何错误。看来已经颇为稳定了。

尝试创建一个带 load balance 功能的 k8s 集群，失败报错：
```
ERROR: ResourceTypeUnavailable: : resources.api_lb<file:///var/lib/kolla/venv/lib/python2.7/site-packages/magnum/drivers/common/templates/lb.yaml>: : HEAT-E99001 Service neutron is not available for resource type Magnum::Optional::Neutron::LBaaS::LoadBalancer, reason: Required extension lbaasv2 in neutron service is not available.
```
好吧，在 kolla 中打开 `enable_neutron_lbaas`，重新部署。这每次修改 `/etc/kolla/globals.yml` 都没有记录，到最后也不知道自己做了哪些修改。

这次重新部署 UI 还是出现浏览器 Angular JavaScript 错误：
```
Error: [$injector:nomod] Module 'horizon.app' is not available! You either misspelled the module name or forgot to load it. If registering a module ensure that you specify the dependencies as the second argument.
```
这个问题太常见，每次都在添加一个新的 horizon 模块/plugin之后发生，清空再重新部署好了。按理说 `kolla/centos-source-horizon:queens` 是 binary 的，不会重新构建啊。主 JS 为 `http://192.168.51.147/static/dashboard/js/3bf910c7ae4c.js`，记下看看这个是否会变。bash 到 horizon 容器中，发现几个 js 文件都是刚刚才产生出来的。暂时不知道办法，清除后重新部署，漫长等待后终于成功。

后来发现创建集群模板那里的 Master LB(--master-lb-enabled) 其实是多 master 的 load balance，和 k8s 里面的 Service LoadBalance  不同。进入 master 节点，查看 /etc/kubernetes 各个配置，都没有`--cloud-provider=openstack`。[cloud_provider_enabled](https://docs.openstack.org/magnum/latest/user/#cloud-provider-enabled) 这个默认不是为 true 么？但是似乎是 Rocky 才有的新功能。我在 magnum-conductor 容器里面查看 configure-kubernetes-master.sh 文件，里面没有考虑到新的标志位。
```
if [ -n "$TRUST_ID" ]; then
    KUBE_API_ARGS="$KUBE_API_ARGS --cloud-config=/etc/kubernetes/kube_openstack__
config --cloud-provider=openstack"
fi
```
这个 shell 脚本其实是由 magnum 传给 heat，在 fedora atomic 上面运行。从 magnum [源代码](https://github.com/openstack/magnum)看，这个 trust_id 贯穿整个流程，为空为什么在创建集群的时候就报错？[troubleshooting](https://docs.openstack.org/magnum/pike/admin/troubleshooting-guide.html#trustee-for-cluster) 这里有讲到 Trustee for cluster 的问题，折腾几次，发现要修改 `/etc/kolla/magnum-*/magnum.conf ` 里面的  **cluster_user_trust** 为 True。不知道这个为什么要默认设置为 false。为了固化配置，修改 /etc/kolla/globals.yml，加上 `enable_cluster_user_trust: "yes"`。

然后把集群里面已经装好的 dashboard service 改为 LoadBalancer 类型的，稍等片刻就能拿到 external IP 了。也试了下 Cinder StorageClass，简单的声明，没有 IP 配置之类，测试成功。有了 cloud provider 获取外部资源确实方便。

OpenStack 到此一游。

![openstack-k8s-network-topology](/images/2018/openstack-k8s-network-topology.png)

这个网络拓扑可以看到集群时由 1 个 master、2 个 minion 节点组成，属于 private 子网，然后通过一个 router 连接外网。private 子网的端口（Ports）除了 3 个节点，还有 DHCP，和 load balancer。

在 horizon 里面，外网或者内网网络是一个 IP 地址池（子网络），通过路由来连接两个网络，图中路由通过两个接口来连接两个网络。而浮动 ip 是外网子网里面的一个 ip，其和内网子网的某个 ip 做了绑定，这样就能通过浮动 ip 直接访问内网虚拟机。

queens 创建出来 k8s 默认版本是 1.9.3，rocky 的是 1.11.1。暂时没有找到配置 k8s 版本的地方。 

### k8s 集群删除失败的问题

一直在删除中，重启服务器后 DELETE_FAILED。进入容器查看 /var/log/kolla/magnum/magnum-condductor.log：
```
ERROR magnum.drivers.heat.driver [req-b693] Cluster error, stack status: DELETE_FAILED, stack_id: c8789bac-6957-48f1-8dd7-94124073c996, reason: Resource DELETE failed: Conflict: resources.network.resources.extrouter_inside: Router interface for subnet 50b433da-d225-413f-89b8-56d94acd7bb0 on router fd2120d9-4599-4a0a-acb0-c237d5bbc126 cannot be deleted, as it is required by one or more floating IPs.
```
查看 floating IP，只有一个 Dashboard load balance 的还没有删除。浮动 IP 手工释放，但是 load balance 挺难删除，相互依赖，摸索后发现最后删除次序为：`pool's healthmonitor —> pool —> listener -> LB -> k8s network`。

Magnum 是如何知道这个浮动 ip 资源是属于 k8s 集群(这里叫stack)的呢？并没有 db 保存这些数据啊？还是 heat 做的？为什么创建集群时创建的 etcd & api LB 都可以删除，dashboard 的却不能？资源都属于一个完整的 stack 范围，这个 stack 和 AWS 的是一样的。
```
openstack stack list
openstack stack resource list foo
```
全部都在这里，magnum 只是一个支持多集群的工具而已。更多命令看 [heat 文档](https://docs.openstack.org/python-heatclient/latest/man/heat.html)。创建好集群后，各资源为：

| resource_name               | physical_resource_id                 | resource_type                                                                        | resource_status | updated_time         |
|-----------------------------|--------------------------------------|--------------------------------------------------------------------------------------|-----------------|----------------------|
| etcd_lb                     | 0fe47743-fa07-488d-ad2a-4975b2f18825 | file:///usr/lib/python2.7/site-packages/magnum/drivers/common/templates/lb.yaml      | CREATE_COMPLETE | 2018-12-10T03:38:08Z |
| kube_masters                | 51ff11de-11f8-47c2-aa99-9c01a223e36f | OS::Heat::ResourceGroup                                                              | CREATE_COMPLETE | 2018-12-10T03:38:07Z |
| network                     | 0d8ebd19-fad5-4be6-bcf8-121a81a73f31 | file:///usr/lib/python2.7/site-packages/magnum/drivers/common/templates/network.yaml | CREATE_COMPLETE | 2018-12-10T03:38:08Z |
| api_lb                      | ce7a4d96-0f13-4fa2-8b5f-5f204aa5330f | file:///usr/lib/python2.7/site-packages/magnum/drivers/common/templates/lb.yaml      | CREATE_COMPLETE | 2018-12-10T03:38:08Z |
| secgroup_kube_minion        | 97c19df9-c9dc-4466-9c01-463aa3c19932 | OS::Neutron::SecurityGroup                                                           | CREATE_COMPLETE | 2018-12-10T03:38:07Z |
| nodes_server_group          | 73e54288-535d-4564-b46c-ceb2adc18e7d | OS::Nova::ServerGroup                                                                | CREATE_COMPLETE | 2018-12-10T03:38:08Z |
| secgroup_kube_master        | cf148deb-ffd2-474e-aecf-6f913f7f0802 | OS::Neutron::SecurityGroup                                                           | CREATE_COMPLETE | 2018-12-10T03:38:07Z |
| kube_minions                | 8eb65593-bb46-40dc-b98a-90b07878035f | OS::Heat::ResourceGroup                                                              | CREATE_COMPLETE | 2018-12-10T03:38:07Z |
| api_address_floating_switch | 06644da0-8e50-4512-9e97-458263924be9 | Magnum::FloatingIPAddressSwitcher                                                    | CREATE_COMPLETE | 2018-12-10T03:38:07Z |
| api_address_lb_switch       | a941f98d-a2fe-4077-a375-5fdde66982f8 | Magnum::ApiGatewaySwitcher                                                           | CREATE_COMPLETE | 2018-12-10T03:38:07Z |
| etcd_address_lb_switch      | f544d40b-6f4a-46be-a29d-6f63e35aac7f | Magnum::ApiGatewaySwitcher                                                           | CREATE_COMPLETE | 2018-12-10T03:38:07Z |

可以看到有 api 和 etcd 的 LB。手工设置 Dashboard Service Type 为 LoadBalancer 后，上面列表并没有改变，问题可能出现在这里，看上去只有纳入 heat 管理的资源才会被正确的删除。magnum 创建了 subnet，所有的 Ports （node & LB）都属于这个子网，删除这个网络必须先移除所有的 Ports，k8s 创建 LB (直接调用 OpenStack API)的 magnum 觉得不属于自己管理，所以就不会主动删除，这导致整个网络也无法删除。上面表格里面有个资源类型,比如 LB 的`/usr/lib/python2.7/site-packages/magnum/drivers/common/templates/lb.yaml`，可能 magnum（或者 heat）严格按照这个来进行创建和删除，这有点像 k8s。

删除上面集群，`openstack stack resource list` 变为：

| resource_name | physical_resource_id                 | resource_type                                                                        | resource_status | updated_time         |
|---------------|--------------------------------------|--------------------------------------------------------------------------------------|-----------------|----------------------|
| network       | 0d8ebd19-fad5-4be6-bcf8-121a81a73f31 | file:///usr/lib/python2.7/site-packages/magnum/drivers/common/templates/network.yaml | DELETE_FAILED   | 2018-12-10T03:38:08Z |

其他的都已经删除。`heat event-list mystack` 也可以查看删除失败原因，其实上面所有信息在 Horizon Orchestration 界面上看得更清楚，😄 

![openstack-heat-stack-graph](/images/2018/openstack-heat-stack-graph.png)

这个可视化编排工具和 AWS 的就非常像了，到底能起多大作用就不得而知了。

疑问：块设备（cinder）是属于哪个资源类型？也会存在同样问题。一种解决办法当 k8s 通过 cloud provider 在 OpenStack 中创建资源时，填入一个新的字段：k8s cluster id，删除时根据 id 找到 lb 和 volume，全部删除。具体查看 [openstack_loadbalancer.go](https://github.com/kubernetes/cloud-provider-openstack/blob/master/pkg/cloudprovider/providers/openstack/openstack_loadbalancer.go) , [openstack_volumes.go](https://github.com/kubernetes/cloud-provider-openstack/blob/master/pkg/cloudprovider/providers/openstack/openstack_volumes.go) 和 `magnum/api/controllers/v1/cluster.py` [delete 方法](https://github.com/openstack/magnum/blob/master/magnum/api/controllers/v1/cluster.py#L559)。更完美的方法应该是 Cloud Provider 调用 heat 来创建资源，也就是对 stack 进行操作。不然为什么自动创建的 etcd & api LB 可以删除呢？但是 heat 是根据 template 来创建 stack，所以[修改](https://docs.openstack.org/newton/user-guide/cli-create-and-manage-stacks.html)可能没那么容易。

### Ceph

感觉相关依赖没有做好，后面加 Ceph，前面创建好的 Cinder 容器没有重建，容器里面的配置都没有修改，这怎么能行呢？清除后重建集群。登录到后发现 cinder-api 下面还是没有 /etc/ceph/ceph.conf 文件，cinder-volume 有，ceph status 无法登录。ceph-mgr 容器运行 `ceph osd pool ls` 返回四个已经创建好的 pool：images, volumes, backups, vms。ceph -s 返回 0 kB used, 0 kB / 0 kB avail。日。

https://docs.openstack.org/kolla-ansible/latest/reference/storage/ceph-guide.html 这里有详细配置，原来这个需要给硬盘加标签，然后 kolla 才会把这个硬盘分配给 Ceph。我只运行：

    parted /dev/sdb -s -- mklabel gpt mkpart KOLLA_CEPH_OSD_BOOTSTRAP 1 -1

还是不行，`cinder service-list` 显示 cinder-volume  ms1@rbd-1 是 down 的状态。但是我看 kolla/centos-source-cinder-volume:rocky 这个容器已经起来。现在问题是几种方法都没有在 docker ps 中看到 ceph-osd/ceph-rbd 容器。再次细看文档：all-in-one 情况下，需要设置 `osd pool default size = 1`，但是没有 /etc/kolla/config/ceph.conf 这个文件，修改 `/usr/share/kolla-ansible/ansible/roles/ceph/templates/ceph.conf.j2`，重新 deploy 后已经能看到 /etc/kolla/ceph-osd/ceph.conf 里面有我加的配置。但是还是不行，容量还是为 0 ，/dev/sdb 根本没有考虑进去。
换成 Queens 版本，因为这个没有 Bluestore，也不知道是不是这个原因。再不行得看 ansible 代码了。
http://docs.ceph.com/docs/master/start/quick-ceph-deploy/ 这里创建 rbd 都是直接命令行，没有放到配置里面。
不行，/usr/share/kolla-ansible/ansible/roles/ceph/tasks/start_osds.yml 创建 osd 的脚本，但是如何知道运行结果呢？kolla-ansible 运行只输出到屏幕，没有地方看全部日志，可能我没找到。启用 verbose，使用命令 

    kolla-ansible ...  - v | tee log

果然发现：
```
TASK [ceph : Looking up disks to bootstrap for Ceph OSDs]  *********
ok: [localhost] => {"changed": false, "cmd": ["docker", "exec", "-t", "kolla_toolbox", "sudo", "-E", "ansible", "localhost", "-m", "find_disks", "-a", "partition_name=KOLLA_CEPH_OSD_BOOTSTRAP_BS match_mode='prefix' use_udev=True"], "delta": "0:00:01.454122", "end": "2018-11-27 21:46:51.368490", "failed_when_result": false, "rc": 0, "start": "2018-11-27 21:46:49.914368", "stderr": "", "stderr_lines": [], "stdout": "localhost | SUCCESS => {\r\n    \"changed\": false, \r\n    \"disks\": \"[]\"\r\n}", "stdout_lines": ["localhost | SUCCESS => {", "    \"changed\": false, ", "    \"disks\": \"[]\"", "}"]}
```
返回空数组，这是没有找到合适的磁盘了？**KOLLA_CEPH_OSD_BOOTSTRAP_BS** 为什么是这个？这个是 bluestore，应该是 KOLLA_CEPH_OSD_BOOTSTRAP。这个分区名字是在 /usr/share/kolla-ansible/ansible/roles/ceph/defaults/main.yml 中决定的，接着由 ceph_osd_store_type 决定，在 /usr/share/kolla-ansible/ansible/group_vars/all.yml 这个默认是 bluestore。好的，我在 /etc/kolla/globals.yml 加上 ceph_osd_store_type: “filestore"。重新发布，现在会停在出错的地方，错误很隐晦，手工在 kolla-toolbox container 里面运行：

    ansible localhost -m find_disks -a "partition_name=KOLLA_CEPH_OSD_BOOTSTRAP match_mode='prefix' use_udev=True”

重现错误：
```
localhost | FAILED! => {
    "changed": false, 
    "failed": true, 
    "msg": "UnicodeDecodeError('ascii', '\\xe6\\x96\\xb0\\xe5\\x8a\\xa0\\xe5\\x8d\\xb7', 0, 1, 'ordinal not in range(128)')"
}
```
有对应磁盘反而报错，似乎是字符集的问题，问题是这个看不懂。下载源码 [find_disk.py](https://github.com/openstack/kolla/blob/master/docker/kolla-toolbox/find_disks.py)，稍作修改，本地运行，发现磁盘有个 LABEL **新加卷**，导致出错。`parted /dev/sdb` 这个命令默认就会产生这个 label。折腾各种命令来修改 label，最后发现这个『新加卷』是原来的 Windows 磁盘，parted 并不会删除旧有分区。mkfs.ext4 格式化之，现在 OK 了！检查最后成功状态：
```
[root@ms1 fan]# lsblk /dev/sdb
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sdb      8:16   0 238.5G  0 disk 
├─sdb1   8:17   0 233.5G  0 part /var/lib/ceph/osd/6fce7aad-0b42-4a0e-bdca-8bb7c9ed7135
└─sdb2   8:18   0     5G  0 part 
```
sdb1 下面都是散放的文件，这个就是 filestore 的意思？现在可以看到有个 kolla/centos-source-ceph-osd:queens 容器在运行。

安装好后，磁盘 label KOLLA_CEPH_OSD_BOOTSTRAP 会被去掉，所以每次清空再次部署时需要自己加上。

[Kolla集成外接ceph存储](https://blog.csdn.net/dylloveyou/article/details/79114741) 集成到我原来创建好的 Rook Ceph 上去？滑稽。

启用 ceph 后，kolla 也会安装 ceph dashboard，也就是 ceph-mgr docker image，一个 web 的监控 UI。容器映射端口是多少呢？奇怪的是用 `docker port` 返回空，`netstat -tlnp` 才找到端口为 7000。

默认 OpenStack 会创建的 ceph pool 有 images, volumes, backups, vms。images 保持 glance 里面的 Linux Cloud Image，和 `glance image-list` 返回一样，raw 或者 qcow2 格式。vms 放 vm 节点的启动磁盘（这个不知道如何用命令查看）。volumes 则是 vm 节点的扩展磁盘，和 `openstack volume list` 返回一样。

### log

[Central Logging](https://docs.openstack.org/kolla-ansible/latest/reference/logging-and-monitoring/central-logging-guide.html) 原来已经有这个东西。kolla ansible 的部署日志没法记录吧，那时候日志服务还没好。

Kibana 没法起来：`/usr/local/bin/kolla_start: line 18: /usr/share/kibana/bin/kibana: No such file or directory` 这什么鬼，好吧，binary 镜像没法自己改吧。自己下载 Kibana 本地运行个，毕竟只是个 UI 端，还好 Elasticsearch 能工作。修改配置让其后端连接 kolla 创建好的 Elasticsearch。启动出现错误：`Status changed from yellow to red - This version of Kibana requires Elasticsearch v6.5.1 on all nodes. I found the following incompatible nodes in your cluster: v2.4.6 @ 192.168.51.147:9200 (192.168.51.147)` 版本不兼容啊，查看其[兼容性 matrix](https://www.elastic.co/support/matrix#matrix_compatibility), 下载对应 4.6.4 版本。

现在 Kibana 里面是可以看到一堆 log 了，但是对于 magnum，只有 magnum-api，没有 magnum-conductor。后来发现要在 magum.conf 里面设置 debug=True。

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

### 部署到 Neutron 时候挂起

一次成功部署后，重启机器，主端口连接不上。清空重新部署，运行到 Running Neutron bootstrap container，ssh 网络断开，console 登录查看最后日志为：

```
TASK [neutron : Running Neutron bootstrap container] ******************************************************************************************************
changed: [localhost -> localhost] => {"changed": true, "result": false}

TASK [neutron : Running Neutron lbaas bootstrap container] ************************************************************************************************
fatal: [localhost -> localhost]: FAILED! => {"changed": true, "msg": "'Traceback (most recent call last):\\n  File \"/tmp/ansible_kolla_docker_payload_e0UJ1B/__main__.py\", line 874, in main\\n    result = bool(getattr(dw, module.params.get(\\'action\\'))())\\n  File \"/tmp/ansible_kolla_docker_payload_e0UJ1B/__main__.py\", line 666, in start_container\\n    self.pull_image()\\n  File \"/tmp/ansible_kolla_docker_payload_e0UJ1B/__main__.py\", line 507, in pull_image\\n    repository=image, tag=tag, stream=True\\n  File \"/usr/lib/python2.7/site-packages/docker/api/image.py\", line 400, in pull\\n    self._raise_for_status(response)\\n  File \"/usr/lib/python2.7/site-packages/docker/api/client.py\", line 248, in _raise_for_status\\n    raise create_api_error_from_http_exception(e)\\n  File \"/usr/lib/python2.7/site-packages/docker/errors.py\", line 31, in create_api_error_from_http_exception\\n    raise cls(e, response=response, explanation=explanation)\\nAPIError: 500 Server Error: Internal Server Error (\"Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)\")\\n'"}

NO MORE HOSTS LEFT ****************************************************************************************************************************************
	to retry, use: --limit @/usr/share/kolla-ansible/ansible/site.retry

PLAY RECAP ************************************************************************************************************************************************
localhost                  : ok=311  changed=175  unreachable=0    failed=1 
```
禁用掉 lbaas 也没用。因为是在部署 neutron，所以应该是网络配置出现问题导致后面没法拉镜像。尴尬。为什么不先下载所有镜像然后开始部署？干净 centos 部署没有问题，几次之后就会出现这个问题。有 iptables 之类的残留？[这里](https://www.reddit.com/r/openstack/comments/8zmvia/the_network_problem_with_kollaansible/e2letmw/)和[这里](https://ask.openstack.org/en/question/93376/during-kolla-deploy-when-neutron-comes-up-networking-goes-down/)都有讨论这个问题，似乎 kolla 会创建一个 br-ex 网桥来做外部通信。后来尝试在宿主机中禁用 `neutron_external_interface`，也就是关掉 DHCP 获取 IP，问题消失，😂

### Rocky 创建 k8s 集群失败
`CREATE aborted (Task create from SoftwareDeployment "enable_cert_manager_api_deployment" Stack "cai-7fzsht5k5dzv-kube_masters-zmbdkz4oxa5s-0-4zytyo6ftz4h" [3bb2ae99-ee48-41e7-b4d7-a38c93a2da41] Timed out)`
在 master 节点上面运行 `journalctl | grep runc`，
```
novalocal runc[2745]: E1211 03:57:42.946464       1 reflector.go:205] k8s.io/kubernetes/cmd/kube-scheduler/app/server.go:176: Failed to list *v1.Pod: Get http://127.0.0.1:8080/api/v1/pods?fieldSelector=status.phase%21%3DFailed%2Cstatus.phase%21%3DSucceeded&limit=500&resourceVersion=0: dial tcp 127.0.0.1:8080: connect: connection refused
novalocal runc[2849]: W1211 03:57:43.388424       1 proxier.go:469] Failed to load kernel module ip_vs with modprobe. You can ignore this message when kube-proxy is running inside container without mounting /lib/modules
novalocal runc[2468]: Source [heat] Unavailable.
novalocal runc[2406]: /var/lib/os-collect-config/local-data not found. Skipping
novalocal runc[2468]: publicURL endpoint for orchestration service in null region not found
```
如此之多错误？运行 `runc list` 可以看到 atomic 上面 heat 是作为一个容器运行在 master node 上面，查看 log 用 `journalctl --no-pager -u heat-container-agent`。对于 publicURL endpoint for orchestration service in null region not found 的问题，[这里](https://ask.openstack.org/en/question/7652/publicurl-endpoint-for-orchestration-not-found/)有解释，我本地试了下，`openstack endpoint list` 包含 heat，Service Type：orchestration，`openstack service list` 里面也包含 heat，难道是因为 RegionOne 没有传给 heat agent？可能因为 heat 依赖 keystone 来找到所有的注册信息。

### Think
* Ansible 是幂等的，也就是说反复部署不会对功能造成影响，这个是理想情况。
* Docker 对宿主机的网络和设备全面接管，和独立运行的程序没啥差别。用容器部署比直接程序更简便么？可能隔离性更好，不需要安装包，对宿主机操作系统影响不大。另外：其配置（/etc/kolla/）和运行时（容器）是隔离开的，符合 12 法则应用理论。
* Docker 用的不错。那用了 Docker 还用 OpenStack vm 干啥呢？技术变化太快，总的来说：OpenStack plays the role of the overall data center management. KVM as the multi-tenant compute resource management, and Docker containers as the application deployment package. 容器另外一个问题是**强隔离**还不够好，vm 能弥补这个缺点。
* 直接用 Docker，出了错只能直接操作 Docker 调试，显然用 k8s 更好些，但牵涉到网络、存储这个问题就更复杂了。
* 漫长的部署居然没有写日志的地方，我只找到使用管道 `tee` 的方法。
* kolla 部署了大量镜像，这些镜像有缓存么？`docker images ls` 没有看到任何镜像。
* kolla 用 Docker 方法部署 OpenStack，目的是为了简化，但是坑也比较多，还要注意各种参数。


