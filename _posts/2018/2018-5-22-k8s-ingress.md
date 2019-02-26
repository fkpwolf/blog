---
layout: post
title:  "Kubernetes Ingress"
date:   2018-5-22 14:20:33
categories:
  - cloud
  - network
typora-root-url: ../../../blog
---
### 介绍和配置
* <https://github.com/kubernetes/ingress-nginx/blob/master/docs/examples/index.md> 很多认证的例子 
* 官方ingress的中文翻译 <https://www.kubernetes.org.cn/1885.html> 
* Nginx Ingress 文档 <https://kubernetes.github.io/ingress-nginx/> 
* `helm install stable/nginx-ingress --set controller.hostNetwork=true` Kubeadm 安装的集群中，Helm 安装似乎必须得用这种方式，因为没有 cloud provider。

两个 nginx web server 的[例子](https://blog.frognew.com/2017/04/kubernetes-ingress.html) 很简单，但是这个里面的 hostname 如何配置？按照 monocular（ `kubectl edit ing monocular-monocular` ）配置了个无 host 的： 
```yaml
apiVersion: extensions/v1beta1 
kind: Ingress 
metadata: 
  name: nginx1-8-ingress 
  namespace: default 
spec: 
  rules: 
  - http: 
      paths: 
      - backend: 
          serviceName: nginx1-7 
          servicePort: 80 
        path: /
```
然后访问 https://192.168.51.11/n 就会有 nginx 的欢迎页面。https://192.168.51.11/ 还是monocular ui。~~注意同一namespace下需要有个nginx-ingress-controller，否则访问会有503的错误。~~ 不需要，整个系统一个就够了。整个ingress还是https，不知道这个是哪里配置的。nginx成功后，转战dashboard，不成功。后来参考 [nginx: How to enable dashboard on a sub-path](https://github.com/kubernetes/ingress-nginx/issues/683#issuecomment-361042654) 还要这么复杂的配置，可能是因为这个backend是https的。 

起了多个nginx-ingress-controller后，发现每个的 /etc/nginx/nginx.conf 其实都是一样的。所以如果根目录/给monocular ui占了，dashboard 只能用 sub path 了。用 host 方式应该更好，但是 host 应该客户端要自己修改 hosts 吧，如果没有 DNS 的话。 

今天在公司同样部署了一遍，发现还是有问题。查看 /etc/nginx/nginx.conf，公司里面都没有 rewrite，又和上面一样问题？发现 nginx ingress 版本和家里的不一致。删除之，运行 helm repo update，再 install 最新版本，找不到安装[指定版本的命令](https://docs.helm.sh/helm/)，不知道为什么不能用 --version？折腾几次才好。现在 dashboard 好了，monocular 又不行，没有 rewrite，对比 yaml： 
```
    "ingress.kubernetes.io/rewrite-target": "/", 
    "nginx.ingress.kubernetes.io/rewrite-target": “/" 
```
下面是dashboard用的版本，换成这个就好了。这 nginx-ingress 变化的也太大了。 

Monocular 的 ingress： 
```yaml
spec:
rules:
- http:
paths:
- backend:
serviceName: exacerbated-parrot-monocular-ui
servicePort: 80
path: /
- backend:
serviceName: exacerbated-parrot-monocular-api
servicePort: 80
path: /api/
```
这个 API 是这样访问的：`curl 10.105.184.166/v1/releases`

一个 ingress 里面的转发 service 只能在同一个 namespace 下面（安全的考虑），这个不灵活性太差了么？当然，可以放到一个文件里面放条 ingress。好的地方在于修改了 ingress ，nginx 会马上生效。赞。如果每个 ingress 都是分离的，但是如果 path 一样，会冲突，而且也很难调试。 

`dig nip.io` 这个提供了 host 的方法，这样每个 host 的配置都是分离的，应用也可以独占 root / context。不知道 Ajax 能否发送到不同的host（DNS  ROOT 一样），比如：dashboard.192.168.51.11.nip 向后端发送 a.192.168.51.11.nip.com AJAX. 或者中间再转发一道？从配置上看，nginx ingress 是推荐 host 方案的。 

Ingress Server 里面有 log： 
```
error obtaining service endpoints: error getting service kube-system/exacerbated-parrot-monocular-api from the cache: service kube-system/exacerbated-parrot-monocular-api was not found 
```
这个 api 是在 default namespace 里面啊？ 这个 ingress 转发的 service 如果不在指定的 namespace 里面是不会转发的，返回404。这种隔离还是可以的。 "kubernetes.io/ingress.class": "gitlab-nginx” 这个可以做隔离，这样可以安装多个 nginx，每个处理不同的 class，这个 class 名字可以随意命名。 

查看其容器文件系统 /etc/nginx/nginx.conf 文件，service 会解析成对应的 pod IP 集合，这样就绕开了 kubelet proxy，性能更好。这是准备取代之么？那他就必须监听 service 的变化了。 

现在发现 nginx controller 安装到哪个节点上面就只能通过这个节点访问，还是需要绑定到某个 external ip？ 如此一来，如果每个 node 都安装 controller，那外面还得再套一层 vip 才能组成 HA？当然，外面的 HA 不需要做复杂的 rule rewrite。 

Helm install stable/nginx-ingress 能否在每个节点上都安装一个呢？守护进程集？浪费了点。 
```
[centos@k8s-1 golang-http]$ kubectl get svc jxing-nginx-ingress-controller -n kube-system 
NAME                             TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE 
jxing-nginx-ingress-controller   LoadBalancer   10.110.233.125   <pending>     80:31174/TCP,443:32420/TCP   5d 
```
这个没有外部 IP？一般情况下在 AWS 或者 Google Cloud 才有 LoadBalancer 集成。 

magnum 上面创建有外部 IP，但是这个 IP 既不是 magnum cluster-list 里面显示的 external_vip，也不是 nova list 里面显示的 floating ip。那是从哪里获取的？我从 LB IP 或者 直接访问 node ip 都可以访问 dashboard，后者的 node ip 应该是 nginx-ingress 安装的 node ip。这个外部 IP 是 OpenStack 的网络环境给的？其如何探测外部网络控制器？ 
```shell
[root@p1-u55snrcux-0-i2y6p7rdsmyj-kube-master-wghdy7r4yg7q ~]# kubectl get svc ingress-nginx -n ingress-nginx -o yaml
apiVersion: v1
kind: Service
metadata:
  name: ingress-nginx
  namespace: ingress-nginx
spec:
  clusterIP: 10.254.65.105
  externalTrafficPolicy: Cluster
  ports:
  - name: http
    nodePort: 30389
    port: 80
    protocol: TCP
    targetPort: 80
  - name: https
    nodePort: 31697
    port: 443
    protocol: TCP
    targetPort: 443
  selector:
    app: ingress-nginx
  sessionAffinity: None
  type: LoadBalancer
status:
  loadBalancer:
    ingress:
    - ip: 10.0.120.145
```
虽然上面有 IP，但是这个是自动获取的。这里结合外部 DNS 可以直接在访问权那里看到 ingress 的 DNS？ 

使用 Ingress 访问 Dashboard 并且去掉 HTTPS(ssl-passthrough) 方法： 
```yaml
[centos@k8s-1 ~]$ cat dashboard-ingress.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: kubernetes-dashboard-ingress
  namespace: kube-system
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/secure-backends: "true"
    ingress.kubernetes.io/ssl-passthrough: "true"
    nginx.ingress.kubernetes.io/rewrite-target: "/"
spec:
  rules:
  - host: dashboard.192.168.51.12.nip.io
    http:
      paths:
      - backend:
          serviceName: kubernetes-dashboard
          servicePort: 443
        path: /
```
这里面我发现必须使用 hostname 这种方式。 
```yaml
apiVersion: extensions/v1beta1 
kind: Ingress 
metadata: 
  name: kubernetes-dashboard-ingress 
  namespace: kube-system 
  annotations: 
    kubernetes.io/ingress.class: "nginx" 
    nginx.ingress.kubernetes.io/secure-backends: "true" 
spec: 
  rules: 
  - http: 
      paths: 
      - backend: 
          serviceName: kubernetes-dashboard 
          servicePort: 443 
        path: / 
```
如果要使用 https 访问，上面配置中不能使用 host，否则都是 404 default backend。 

使用 Nginx 的[自定义代码](https://github.com/kubernetes/ingress-nginx/tree/master/docs/examples/customization/configuration-snippets)直接返回 mock 数据：
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: customization-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/configuration-snippet: |
      more_set_headers "Content-Type: application/json";
      return 200 '{"data":[]}';
spec:
  rules:
  - host: cust.192.168.51.12.nip.io
    http:
      paths:
      - backend:
          serviceName: nginx-ingress-default-backend
          servicePort: 80
        path: /
```
这里必须弄个backend，否则 kubectl apply 无法通过校验，可以用默认 backend。

### 内部机制

[How it works](https://kubernetes.github.io/ingress-nginx/how-it-works/)，首先，需要监听 k8s Ingress 资源变化，这里用了 Informers，『Informer 在初始化的时，先调用 Kubernetes List API 获得某种 resource 的全部 Object，缓存在内存中; 然后，调用 Watch API 去 watch 这种 resource，去维护这份缓存; 最后，Informer 就不再调用 Kubernetes 的任何 API。』[Kubernetes Informer 详解](https://www.kubernetes.org.cn/2693.html)，属于 k8s client-go 的技术。生成 nginx.conf 时也会做前后对比，如果一样则不 reload。

![ingress-work](/images/2018/ingress-work.jpg)

<https://my.oschina.net/caicloud/blog/829365>

然后如果只是 endpoint 修改，为了减少 nginx 频繁 reload，使用 Lua OpenResty 来做动态的路由变更。 

nginx-ingress-controller 是直接在容器中起了一个 nginx 进程，这个可以在容器命令行里面看到： 
```shell
root@k8s-1:/# ps aux   
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0   4040   360 ?        Ss   May21   0:00 /usr/bin/dumb-init /nginx-ingress-controller --default-backend-service=d
root         5  0.3  0.6  41304 23740 ?        Ssl  May21  70:43 /nginx-ingress-controller --default-backend-service=default/crabby-aardv
root        21  0.0  0.8 138812 31904 ?        S    May21   0:29 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
nobody   10349  0.0  0.9 401344 38236 ?        Sl   14:47   0:00 nginx: worker process
nobody   10350  0.0  0.9 401344 36612 ?        Sl   14:47   0:00 nginx: worker process
```
容器有个很少用的定义 hostPort: 80，直接占用 80 端口，这个很霸道，所以我们能直接 k8s-2 这种不加端口方式访问服务，这种就在拿不到 LoadBalance IP 情况下还能访问服务。 

nginx-ingress-controller 如何让 nginx reload？查看代码 ingress-nginx/internal/ingress/controller/nginx.go, 里面有 nginxExecCommand("-s", "reload”)，原来是直接调用命令。 
```
/nginx-ingress-controller 
--default-backend-service=default/crabby-aardvark-nginx-ingress-default-backend 
--election-id=ingress-controller-leader 
--ingress-class=nginx 
--configmap=default/crabby-aardvark-nginx-ingress-controller 
```
这个是 controller 的启动参数，里面配置了 default backend，这个是默认的 404 返回。为什么要单独做成一个 pod 呢？放在 controller 里面不可以？我能想到唯一优点就是多个 ingress-controller 可以共用一个 default backend service，如果 ingress controller pod 挂了也能转发就厉害了。 

上面有个 `election-id` 参数，传入的是个 configmap。 
```sh
kubectl edit configmap ingress-controller-leader-nginx -n kube-system 
annotations: 
    control-plane.alpha.kubernetes.io/leader: '{"holderIdentity":"jxing-nginx-ingress-controller-6d45cdfdc8-474vb","leaseDurationSeconds":30,"acquireTime":"2018-09-20T03:58:08Z","renewTime":"2018-10-09T02:42:30Z","leaderTransitions":34}’ 
```
jxing-nginx-ingress-controller-6d45cdfdc8-474vb 是一个 pod id，使用 configmap 就完成 leader election？既然流量是负载均衡的，这个 election 有啥用？这个参数的[定义](https://kubernetes.github.io/ingress-nginx/user-guide/cli-arguments/)是『Election id to use for Ingress status updates. (default "ingress-controller-leader”)』。跟踪代码，`ingress-nginx/internal/ingress/status/status.go` 这里选举了 leader 来负责更新 ingress 状态，比如 kubectl get ingress 返回的 address。里面依赖的 leaderelection 是 k8s client-go 提供的一个简单的方法来选举 leader，leader 会不断刷新 renewTime，如果自己挂了，其他轮询的 controller 会变为 leader，变为 leader 的 pod 在其 OnStartedLeading callback 中会开始更新 ingress status task。因为每个 ingress controller 的状态都是一样的，所以谁来更新都一样，但是没有必要每个人都来更新。 

[Simple leader election with Kubernetes and Docker](https://kubernetes.io/blog/2016/01/simple-leader-election-with-kubernetes/) 这个是一个使用这个类库的 hello world。kube-controller-manager 也使用了类似机制，有个 `--leader-elect=true` 参数，在 k8s HA 会有用到。这里方法是 [分布式系统理论基础 - 选举、多数派和租约](http://www.cnblogs.com/bangerlee/p/5767845.html) 里面谈到的租约方法，『租约机制确保了一个时刻最多只有一个leader，避免只使用心跳机制产生双主的问题。在实践应用中，zookeeper、ectd可用于租约颁发。』

其 build 系统颇为复杂，最上层的镜像定义为 rootfs/Dockerfile，定义了入口 CMD ["/nginx-ingress-controller”]。然后我猜想这个镜像是基于自己定义的一个 nginx 镜像 images/nginx/rootfs/Dockerfile，其 images/nginx/README.md 定义了这个镜像的组成，里面包含了很多 nginx 模块比如 openresty set-misc-nginx-module。然后这个镜像是基于 [debian-base](https://github.com/kubernetes/kubernetes/tree/master/build/debian-base)，一个 k8s 对 Debian 的精简版本，删除了很多东西，只有 40MB。这几个组成了一个层级的容器镜像，这样每个部分可以独立演化，也减小了本地镜像的大小：比如开发中 nginx-ingress-controller 有多个版本（这个是常见情况），但是底层都没变，这样的镜像总体存储会小很多。 

使用 NodePort 后，~~服务都可以直接从 master:nodeport 访问，不需要 proxy~~。但是这样 master 不就压力大了么？NodePort 可以从每个运行 backend pod 的节点上面访问，也就是是说每个节点都打开一个同样数字的端口，kube-proxy 做的？没错，kube-proxy 其实就是管理 service 的访问入口，包括集群内 Pod 到 Service 的访问和集群外访问 service。ingress 是和 kube-proxy 平级的，比如 nginx-ingress 就是直接代理到 pod port。

![nodeport](/images/2018/ingress-nodeport.png)

### Heptio Contour

<https://github.com/heptio/contour> 这个公司最近 twitter 一致很活跃，好吧，我来试试这个东西。 

`kubectl apply -f https://j.hept.io/contour-deployment-rbac` 自己部署在自己的 namespace 里面，服务默认是 LoadBalance，这个是 aws ELB 才能用，我改成 NodePort 

`kubectl apply -f https://j.hept.io/contour-kuard-example` 这个会部署一个 kuard demo， 然后访问 http://k8s-1:port 就可以访问了。 
```shell
[centos@k8s-1 ~]$ kubectl get ing
NAME             HOSTS                        ADDRESS   PORTS     AGE
kong-dashboard   dashboard-kong.x.access.ly             80, 443   31d
kuard            *                                      80        1m
```
修改端口为 88 看看，哦，不对，这个是 kuard pod 暴露的端口，改成 88 马上不能访问。不过这个可以看到动态加载的特性。 

试了下，http://k8s-2 也可以访问，因为我安装了另外一个 Ingress controller: Nginx。但是奇怪的是查看 Contour pod log，似乎其只对这条 ingress 进行了监听和处理。 

尝试一个常见的 rewrite 规则 <https://github.com/heptio/contour/blob/master/docs/ingressroute.md#prefix-rewrite-support> ，这个在 Nginx Ingress 里面要用 nginx 特有的复杂规则。 

IngressRoute 自己扩展的么（k8s 官方用来替代 Ingress 的，到现在为止只有 Contour 支持）， 
```shell
[centos@k8s-1 ~]$ kubectl get ingressroute
NAME      FQDN      TLS SECRET   FIRST ROUTE   STATUS     STATUS DESCRIPTION
kuard                            /             orphaned   this IngressRoute is not part of a delegation chain from a root IngressRoute
```
他这个能检测到错误？这个对于调试还是颇有用处的。原来这个说需要根入口的错误是指需要 virtualhost 值，修改后 status 就变成了 valid IngressRoute。好的，我加了 fqdn: kuard.192.168.51.11.nip.io（这里我先删除原来的 Ingress，否则会干扰。），但是访问出错，感觉使用 host name 的时候不能使用端口？好的，我换成 Nginx ingress -》Contour ingressroute => kuard，还是不行，503 Service Temporarily Unavailable，提了个 issue [IngressRoute not work if configure Contour as NodePort type service](https://github.com/heptio/contour/issues/814)。这样就可以: 

    curl -H "Host:contour.192.168.51.11.nip.io" -v contour.192.168.51.11.nip.io:31974 

确实是使用 hostname 时候不能使用端口，看解释是上游 envoy 的 bug，nginx ingress 有类似的问题么？ 

### 其他 API 网关

<https://jimmysong.io/kubernetes-handbook/concepts/traefik-ingress-controller.html> 这个里面推荐是 Traefik，这个似乎比 Nginx 慢，但是 Go 语言写的，更轻量级，<https://traefik.io/> 功能更多。 

Ambassador is an open source Kubernetes-native API Gateway built on Envoy, designed for microservices.<https://github.com/datawire/ambassador> 

Istio's basic ingress controller is very limited, and has no support for authentication or many of the other features of Ambassador. Istio 也支持认证吧。 

Contour is an Ingress controller for Kubernetes that works by deploying the Envoy proxy as a reverse proxy and load balancer. <https://github.com/heptio/contour> 

<https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/annotations.md> 里面有用 OpenResty 只是暴露出来都是 meta 参数  

Kubernetes ingress-nginx customized to run LUA – David Karlsson 定制的方法 [www.davidkarlsson.no/2017/12/24/m-htm/](http://www.davidkarlsson.no/2017/12/24/m-htm/)  

修改Nginx template <https://github.com/kubernetes/contrib/tree/master/ingress/controllers/nginx/examples/custom-template> 文件巨大，不敢改。the template is tied to the Go code，其实就是 Go 语言里面的模板。 

Automated nginx proxy for Docker containers using docker-gen <https://github.com/jwilder/nginx-proxy> 这个项目也用的是template 

Ingress 只能作为前端代理，对于 Docker 里面的代码一般是通过 service name 访问的，这个就绕开了 ingress。而 service mesh 采用了 sidecar 模式，docker 里面代码还是通过该 service hostname 访问，尔后 sidecar 转发。而且 service mesh 控制是双向的，即能控制出口，也能控制入口。所以 istio 这种控制更为全面。 

<https://istio.io/docs/tasks/traffic-management/ingress/> Istio 也提供了 ingress 支持，有自己的模型叫做 Istio Gateway。 

OpenResty最佳实践 <https://moonbingbing.gitbooks.io/openresty-best-practices/content/> 这东西如果只是用来替代Nginx+Tomcat，视乎没有必要，毕竟平时开发还是Java懂的人多些，开发效率也高，也没有必要太讲究性能。 我觉得应用场景在：前段的拦截层，这个层对于后端开发来说是透明的。可以做基于lua的负载均衡、会话校验、缓存的处理。就是说如果这层很轻，用Java footprint太大，则可以使用OpenResty。 

偶遇Lua——在nginx conf中编程 <https://blog.goquxiao.com/posts/2014/12/20/ou-yu-lua-zai-nginx-confzhong-bian-cheng/> 修改 JSON response 的方法。 

Nginx 很快，Lua 就快么？[Top ten things about openresty](http://www.staticshin.com/top-tens/things-about-openresty.html)『Openresty uses luajit to compile lua code. The code compiled with lua jit is damn fast reaching almost c levels of performance.』 

[Kubernetes 中使用 API Gateway 替代 Ingress](https://blog.joway.io/ops/kubernetes-gateway/)，介绍了其他的些 ingress 

<https://github.com/Netflix/zuul/wiki/Getting-Started-2.0> Netflix正式开源其API网关Zuul 2. Java project. 

### External IP / Load Balance
如果 k8s 集群运行在 OpenStack 下面，
```shell
[fedora@kong-s4cfqkyjsyu2-master-0 ~]$ kubectl get service -n kube-system
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                  AGE
heapster               ClusterIP      10.254.67.110    <none>          80/TCP                   1h
kube-dns               ClusterIP      10.254.0.10      <none>          53/UDP,53/TCP,9153/TCP   1h
kubernetes-dashboard   LoadBalancer   10.254.199.103   192.168.51.64   443:31567/TCP            1h
```
可以看到能获取外部 IP，如果运行在没有外部云环境情况下（比如 kubeadm 搭建的集群），EXTERNAL-IP 这栏就显示 `<pending>`。这个 IP 是 OpenStack 的一个浮动 IP，和节点的内网 IP、浮动 IP 都不同。如果 k8s 集群创建时启用了 Master LB，Openstack 也会为 API 和 etcd 创建 LB。一个 load balance 维护了EXTERNAL-IP、端口和后端主机列表的关系。

![k8s-ingress-lb](/images/2018/k8s-ingress-lb.png)
listener 代表上面的关系，pool 则表示后端主机列表，端口就是上面 dashboard service 的内部端口。奇怪的是这个 pool 有两台主机，其实 dashboard pod 只有一个实例，可能是因为这个是 default pool。进入其中某个节点：
```shell
[fedora@kong-s4cfqkyjsyu2-minion-1 ~]$ sudo netstat -tulpn
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      985/sshd            
tcp        0      0 127.0.0.1:10248         0.0.0.0:*               LISTEN      2409/kubelet        
tcp        0      0 127.0.0.1:10249         0.0.0.0:*               LISTEN      2240/kube-proxy     
tcp        0      0 10.0.0.3:10250          0.0.0.0:*               LISTEN      2409/kubelet        
tcp6       0      0 :::22                   :::*                    LISTEN      985/sshd            
tcp6       0      0 :::31567                :::*                    LISTEN      2240/kube-proxy     
tcp6       0      0 :::10256                :::*                    LISTEN      2240/kube-proxy     
udp        0      0 127.0.0.1:323           0.0.0.0:*                           767/chronyd         
udp        0      0 0.0.0.0:68              0.0.0.0:*                           813/dhclient        
udp        0      0 10.0.0.3:8285           0.0.0.0:*                           1944/flanneld       
udp6       0      0 ::1:323                 :::*                                767/chronyd         
```
kube-proxy 在这两个节点都有开放 31567 这个端口，不管这个节点上是否有提供服务。Nginx ingress 似乎比这个智能，其直接转发给真正的 pod。

如果 dashboard 伸缩为两个，运行在两台不同主机上面，load balance 也就需要动态变更后端主机列表了。所以从这里看其运行模式和 ingress 是一样的。这些关系是谁动态维护的？可能是 k8s 向 cloud provider 申请的，cloud provider 则使用 OpenStack API 直接操作其资源。

如果性能要求最高，应该是 Service 直接定义成负载均衡模式，而不用 Nginx Ingress --> Service 模式，但是一般负载均衡器都是要付钱的，所以还是用一个总的 LoadBalance Ingress 或者一个总的 LoadBalance Service（比如 Contour）。有点废话，ingress 也是 service。

