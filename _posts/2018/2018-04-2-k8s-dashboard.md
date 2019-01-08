---
layout: post
title:  "Kubernetes Dashboard 整合"
date:   2018-04-2 14:20:33
categories:
  - cloud
  - web
typora-root-url: ../../../blog
---
### 安装
https://github.com/kubernetes/dashboard
开发环境 https://github.com/kubernetes/dashboard/wiki/Getting-started
* npm i 需要老的 python 环境：export PATH=/System/Library/Frameworks/Python.framework/Versions/2.7/bin:$PATH
* xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance 解决办法： xcode-select --install # Install Command Line Tools if you haven't already.
sudo xcode-select --switch /Library/Developer/CommandLineTools # Enable command line tools
这个是 node-gyp Node.js native addon build tool 需要的 
* export KUBE_DASHBOARD_KUBECONFIG=~/k8s-admin.conf
* http://localhost:3001/ 会打开 BrowserSync，但是我修改了页面并不会自动刷新。还要我修改代码。这个刷新是整个页面刷新，只有 CSS 才会局部刷新。

![](/images/2018/k8s-dashboard-arch.png)

开发环境中 BrowserSync (9090) ---> Dashboard backend (9091) ---> Kubernetes API Server (8080),  ~~这个 backend 也作为静态服务器，这样开发环境中我就没法配置我的代理了。~~其实也可以修改 build/serve.js 添加自己的 proxy，但是要注意的是生产环境下是没有 BrowserSync 的。BrowserSync 只会转发 /api 开头的到 backend。

生产环境下 backend 做了前端资源的代理，并没有用一般的 Nignx 作为代理，那样得起两个 server，然后搭配 ingress。

### 开发

Angular 模块：一个模块包含自己的 router，这样不用统一一个地方定义 router。router 具体参考 <https://ui-router.github.io/guide/states> 

连一个普通的 card 都要用自己包装的 kd-resource-card，这样好么？

REST Call Steps：

1. 通过 stateconfig 获取路由配置 
2. 先运行 config.resolve 里面所有方法并获取数据后 
3. 进入 路由的 controller 的构造函数，准备注入参数所需参数 
4. 参数 {!backendApi.HelmList} helmList 由 stateconfig 里面 config.resolve 来注入 
5. 注入过程中会需要参数 kdHelmListResource，这个由 modules.factory 定义和注入 
6. kdHelmListResource 调用 GET 方法来完成 http.get 调用 

技术特点： 

- Angular 1.6 + Angular Material 1.1，稳定的组合。不过已经在做 migration 升级到最新的 Angular。有提供方法来在[不同版本共存](https://angular.io/guide/upgrade)的 hybrid 应用，不过 router 要换，感觉风险有点大。
- 大量使用标签注入，这个确为 Angular.js 的最大特色了，也方便其测试。参数的注入和参数的传递似乎有冲突，对话框是直接传递。注入只能用在初始化的地方。通过注释注入和声明类的时候注入效果一样，注释的方式更强，可以声明参数类型。gulp scripts:prod 运行时会检查这个 type，但是只是名字上的校验下有没有这个 type？
- 似乎这种 google-closure-compile 搞法是把强类型检查引入标准 JavaScript。如果页面有使用 controller 的方法而改方法没有注释为 @export，开发环境下 template 可以访问，prod 下就不可以了。似乎有的可以: src/app/frontend/helm/charts/install_chart_controller.js，cancel 不用 export 也可以访问。  ~~如果 backendAPI 类型没有定义完整，prod 下这个属性就不会显示，我猜它直接修改了 JavaScript，只会暴露很少的接口。如果 export 错的 type，prod 页面也不会显示相应数据。~~感觉这个 closure-compile 动静好大。postData={chartId: 123} 里面的 chartId 要用引号，否则会变成 ed，有的地方又不会。有时候我加了引号也没用，必须改成名字长点的来混淆，我猜它同名字的会抽取。我日。对于构造函数 constructor 的参数注入，开发环境下我只定义了@param，没问题，但是 prod 下报错 Unknown provider: aProvider <- a，后来发现要对 constructor 加上 @ngInject 签名，我原来搞错了写的 @export，要求这么严格？这个错误也因为是编译后的代码，挺难定位。而且搞这么多为什么不直接用  **TypeScript**？ 
- k8s-dashboard/src/app/externs/backendapi.js 可以把 json 转为所需静态对象？如果 backendapi 没有定义类型而代码里面用了 @suppress {missingProperties} 来忽略，prod 下面会去不到这个属性。
- package.json 里面加入一个依赖后， k8s-dashboard/.tmp/serve/index.html（也就是开发环境下的浏览器源代码） 会自动 include 它。但是我在添加 js-yaml 时不行，后来发现需要在 package.json 里面加上一个 overrides 项："main": "dist/js-yaml.js”，这个是 wiredep 生成依赖项，可能 js-yaml 不标准需要手工指定其 include file。今天在使用 ng-file-upload 时发现其还需要在 module.js 里面 angular.module 加上 ngFileUpload，否则 template directive attribute 不起作用。
- 统一的 action bar，每个子页面可以定制 bar。设计很有意思
- 组件，也即 directive，比如 `<kd-info-card-entry>`，显示键值对，src/app/frontend/common/components/infocard/infocardentry_component.js，通过angular.module(...).component('**kdInfoCardEntry**', infoCardEntryComponent) 这种方式来定义和声明。其 backendapi.js 里面数据类型的定义是根据后台代码生成的，好麻烦，毕竟项目又不是 typescript 的，这样意义何在？还好有现场的可以参考monocular/src/ui/src/app/shared/models/chart.ts。 
- ES6 还不能使用，有点不方便。但是箭头函数可以。奇怪。
- ng-switch & ng-if & ng-repeat 用多了之后，就需要抽取组件了，否则模板太混乱，这个对于 React 也是一样的：React 虽然可以写在 JavaScript 中，但如此一来，嵌套太深。组件在这里并非只为重用，而是分离代码：关注点分离。
- scss 文件只需要和 js 文件保持一致即可，其实 build 只是简单的 merge 所有 scss？
- 有页面的全局加载动画，似乎是和 ui-router 的 resolve 关联
- [i18n](https://github.com/kubernetes/dashboard/wiki/Internationalization)，开发环境要运行 gulp serve:prod 命令后[才能显示中文](https://github.com/kubernetes/dashboard/issues/2104)，也就是在生产模式下。可能开发模式大多数都是英文开发人员。 
- 没有 Redux 的东西了，导致我想做一个全局的加载动画都很困难。用 service？ 
- 每个页面都是一个 module，都包含 state, stateconfig 等好几个文件。创建一个页面可能会比较繁琐，但不建议对这进行优化。不应掩盖细节。可以使用脚手架工具，不要使用封装。 
- 很多地方，比如 state 不对，则链接不能点击。页面无法注入数据，则页面不显示。这里不应该偷偷的掩藏错误。 
- 使用 <http://localhost:8081/#!/alert?namespace=default> 这种没有 context 的 url schema，如果 nginx 转发，规则很简单。在云环境下兼容性更好。 
- Dashboard 调用时，只要调用自己的 API 页面就显示空白，后来发现不能返回数组，只能对象。k8s [API Conventions](https://github.com/kubernetes/community/blob/master/contributors/devel/api-conventions.md) 可能会对这个有明确约束。不是，这个是 $resource 的约束，需要对$resource 进行[定制](http://embed.plnkr.co/tbI7h55l1GyDIsW0HLrE/)，实现 transformResponse 方法。

向导组件：这个在新的 Angular Material 2才有，我使用 <https://github.com/eberlitz/material-steppers>。如果要结合 ui-router，参考[这里](https://stackoverflow.com/questions/27221222/separate-controller-per-tab-in-angular-material-w-ui-router?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)和[这里](https://gist.github.com/movibe/86ccee9d474d8e05017f)。

控制台会报错：Metric client health check failed: the server could not find the requested resource (get services heapster). Retrying in 30 seconds. 

整合 Heapster，<https://github.com/kubernetes/dashboard/wiki/Integrations>，直接用 Helm 安装即可。安装后 pod cpu/内存图表才会显示。Heapster enables Container Cluster Monitoring and Performance Analysis for Kubernetes。Kubernetes 有个出名的监控agent---cAdvisor。在每个kubernetes Node上都会运行cAdvisor，它会收集本机以及容器的监控数据(cpu,memory,filesystem,network,uptime)。在较新的版本中，K8S已经将cAdvisor功能集成到kubelet组件中。每个Node节点可以直接进行web访问。Heapster是一个收集者，将每个Node上的cAdvisor的数据进行汇总。 

现在已经Heapster is **deprecated**，这块功能和Prometheus有重复。有的改用 [metrics-server](https://github.com/kubernetes-incubator/metrics-server)，但是 dashboard 那边还没改啊（ service name 变了），可能 API 是兼容的。 确实，URI 修改下就可以了。

[kube-state-metrics](https://github.com/kubernetes/kube-state-metrics) Heapster 只是收集和转发数据，这个会主动抓取和收集 k8s 的监控信息，应该内容更多点。

今天发现界面 metric 图表显示不了，heapster log：
```
E1030 15:58:05.030468 1 summary.go:97] error while getting metrics summary from Kubelet k8s-3-new(192.168.51.13:10255): Get http://192.168.51.13:10255/stats/summary/: dial tcp 192.168.51.13:10255: getsockopt: connection refused 
```
他会依次到各个节点上拉取数据，但是本身只有一个 pod，单点故障。

这里有 [issue](https://github.com/kubernetes-incubator/metrics-server/issues/77) 解决这个问题，要修改启动的参数，现在是 https。然后：
```
Error in scraping containers from kubelet_summary:192.168.51.13:10250: request failed - "403 Forbidden", response: "Forbidden (user=system:serviceaccount:kube-system:eerie-sparrow-heapster, verb=get, resource=nodes, subresource=stats)”
```
这个说是要修改 [RBAC](https://github.com/stephenmoloney/k8s-scw-baremetal/commit/9f71910055d97909d30e2345be45cd0aa084f6a9)，kubectl edit clusterrole system:heapster，需要加上对资源 nodes/stats 的访问即可。这种直接赋予权限，安全不是纸糊的一样？有点像 Android 的权限申请，安装的时候提出警告，但是对于镜像代码来说是透明的。

UI 的实时数据自动更新还在开发中 <https://github.com/kubernetes/dashboard/issues/2541> <https://github.com/kubernetes/dashboard/pull/1459>，对于简单的可以参考 src/app/frontend/logs/component.js，使用 angular.$interval。

### 构建和发布

<https://github.com/kubernetes/dashboard/wiki/Getting-started>

运行 gulp serve:prod，其会调用 JavaScript 代码格式校验的任务 gulp scripts:prod。如果要 suppress，看[这里](https://github.com/google/closure-compiler/blob/master/src/com/google/javascript/jscomp/parsing/ParserConfig.properties#L147)。gulp serve:prod 运行时没有了代理如何测试呢？因为 prod 运行时 angular 校验更严格，有些问题只能在 prod 下才能显示。本地来个 Nginx？

在 Mac 下打的 Docker image 包，发布后不能运行，似乎 Mac 下的 Go 编译的只能在 Mac 下运行（废话，今天我看到可以跨平台编译，`GOOS=linux GOARCH=arm go build test.go`，这样就可以了，很方便）。官方文档中有方法在 Docker 中编译 build/run-gulp-in-docker.sh，编译的代码是当前的目录的代码，Docker push 会发到本地 Mac 上面，然后 Mac 上再运行 tag+push 就可以了。这个使用方法很有用。其 build 过程中会编译很多的 node.js 包，看到有 gcc 进程。但是这个生产环境应该不需要 Node.js 啊。

build/run-gulp-in-docker.sh 中会先根据 build/Dockerfile 生成一个 kubernetes-dashboard-build-image Docker image，这个 image 会运行 npm install 安装所有依赖，然后会在这个 image 中运行 gulp，这样打出来的包能在 linux 下面使用。而且说是会如果没有更新会使用已有的 image，但是我看到基本每次都会 build 新的，造成过程很漫长。

今天清除了些 docker image 后，运行失败：
```
[10:13:05] Starting 'docker-image:head'... 
/usr/bin/docker: 2: /usr/bin/docker: Syntax error: newline unexpected 
[10:13:05] 'docker-image:head' errored after 64 ms 
[10:13:05] Error: Docker command error, code: 2 
​    at ChildProcess.<anonymous> (/dashboard/build/deploy.js:135:14) 
```
直接运行 docker run -it kubernetes-dashboard-build-image 手工进入这个 image，然后运行 docker，居然报错。 
```
/usr/bin/docker: line 1: syntax error near unexpected token `newline' 
/usr/bin/docker: line 1: `<html>’
```
原来 build/Dockerfile 里面的 RUN curl -sSL https://get.docker.com/builds/Linux/x86_64/docker-1.9.1 > /usr/bin/docker 拉取返回了 403。Docker 官方搞什么鬼？第二天这个 URL 又可以访问了。

原来这个 kubernetes-dashboard-build-image 内部会运行 docker 来 build image，然后这个 image 放在了 host 机器上面的 docker 环境？原来在于build/run-gulp-in-docker.sh命令行参数：
```
docker run \ 
    -it \ 
    --rm \ 
    --net=host \ 
    -v /var/run/docker.sock:/var/run/docker.sock \ 
    ${DOCKER_RUN_OPTS} \ 
    ${DASHBOARD_IMAGE_NAME} \ 
    ${DEFAULT_COMMAND} $@ 
```
这样把 host container 的环境都传入了，并非是 docker in docker。—rm 参数是用来运行后删除 container 的，—net 把 host 所有的 network interface 传入container。-v 即 —volume。

build 过程很慢，因为我们没有 ci/cd，但是热更新非常快：开始老版本的还存在，所以不会马上不会访问，然后新版本发布好了老板本才会删除。用了本地 Docker 库，这个新版本发布和运行起来都很快。

build 过程中会有 brew/node.js 的网络访问，如果失败，镜像还是会成功，也会更新本地 image，但是内容还是旧的。这点不好。似乎没有提交代码 Build 过程也会很快，Image 也是旧的。

有时候 docker push 后 k8s 拉取的 Docker 还是老的，得必须加个版本。可能是 k8s cluster cache 的问题。如何确认运行 Docker 的构建信息呢？总改版本不太好。[这个 issue](https://github.com/kubernetes/kubernetes/issues/33664) 有强制的办法：A solution to force the re-pull of the image is to change the pod-template hash during each build. This can be achieved by adding an environment variable that is altered during build. 然后重新 kubectl apply 一次就可以了，不用先 kubectl delete。

发布后，Dashboard 经常出现 CrashLoopBackOff，然后过会就好了。log 都是好的，只有 kubectl desc 有如下可疑信息：
```
Normal   Killing. 32m (x3 over 34m)   kubelet, vm2 Killing container with id docker://kubernetes-dashboard:Container failed liveness probe.. Container will be killed and recreated.
```
去掉 yaml 中的 **livenessProbe**，似乎运行正常了。这个配置是如果 probe path / 失败，k8s 会 kill 这个容器然后尝试重启。不知道我的根目录 / 访问为什么会失败，和 ingress 有关？ 后来发现原来是因为删除了翻译，浏览器可以访问中文，但是 k8s 探针访问 HTTP request  Accept-Language 是没有设置的，导致访问失败。解决办法是 dashboard backend 设置默认为中文。

发布后，<https://vm1/helm/list/delete_dialog.html> 404 奇怪的为什么会发送这个请求？现在只能把 dialog template 放在 js 当中。

证书的问题：
```
NET::ERR_CERT_INVALID
Subject: kubernetes-dashboard-846b8b6844-mlxjd.kube-system
Issuer: kubernetes-dashboard-846b8b6844-mlxjd.kube-system
Expires on: 293878年1月10日
Current date: 2018年12月8日
PEM encoded chain:
-----BEGIN CERTIFICATE-----
MIIBwTCCAWagAwIBAgIQPRWE/kHIbl8xorbl55Pe4zAKBggqhkjOPQQDAjA8MTow
OAYDVQQDEzFrdWJlcm5ldGVzLWRhc2hib2FyZC04NDZiOGI2ODQ0LW1seGpkLmt1
OGI2ODQ0LW1seGpkLmt1YmUtc3lzdGVthwQKZB0CMAoGCCqGSM49BAMCA0kAMEYC
IQD4Ad9IN01Eb/ENdzY1w+oUXo+HXnuAB+zlGuDS/Bz7jQIhAMd+2bWiX0VeR2VW
F+XikYiQErDOUghEOPwXt2kuF6Bp
-----END CERTIFICATE-----
```
[这里](https://github.com/kubernetes/dashboard/issues/2954)说要重新生成证书，不过这个本身很奇怪，有的时候怎么可以。

### 后端
主要访问 k8s API Server，做了大量的封装，主要是对 API 进行合并的操作，比如在 pod details 这种页面，包含了多个纬度的信息，每个都要一个独立的 request 才能获取到。合并后前端一个 request 能返回更多内容，大大减少了前端开发量。
所有路由都定义在 src/app/backend/handler/apihandler.go 里面
但是也提供逃逸接口 `api/v1/_raw/namespace/name/:name`，这个删除命名空间是没有封装的，可以这样直接调用，当然这种标准的 k8s 资源操作方式才可以这样。如果我们大量使用 CRD，则前端直接调用某个微服务 Ingress 的可能性就会大大减少。

### 整合
需要整合监控告警、镜像仓库、应用商店。

![](/images/2018/dashboard-integrate.png)


### 疑问 / TODO
1. 整合结果也发布成 Helm Chart
2. 加一个全局命令行，能 ssh 到每个节点。这个在功能较少的时候颇有用。这个功能可能在 openstack 已经做了
3. 对于有 UI 的 chart，如何让其自带或者能创建时配置访问 Ingress 端口，有明显的提示
4. 和 k8s 已有搜索的整合
5. 功能可以少，但是要小而精
6. Chart Readme 中的链接应该在新窗口中打开，里面内部会不换行
7. 有时候 monocular rest api 返回很慢，其自己的 ui 也有同样问题。但是其 UI 做了 cache，虽然我觉得没啥必要
8. 依赖的核心 helm 应用应该不能删除，使用 release label，更好的办法使用权限来限制 
9. Helm 发布时要能指定 name 和设置定制参数
10. 和买主机不同，可以自己先在开发项目中一起来，结合大数据项目等业务场景
11. 其 List 重用度很高，既可以单独使用，也可以在 Dashboard 里面合并使用。不知道全局搜索会不会对其生效
12. 创建 drone 时会失败，界面没有给出失败信息。helm 命令行安装也失败，但是在 monocular 原生 ui 里面是可以成功的
13. K8s-ext: monogo 无法连接是没有报错；修改的 configmap 写死在 monitoring下
14. 监控告警的地方最好有几条初始化的样例
15. 如果要参考 DaoCloud 的极简风格，得抛弃 Dashboard，用 React 创建全新应用
16. 原生和改进两套系统并存。目的并非代替，总是开放的
17. 现在监控和告警是集成在 dashboard 里面，如果 dashboard 也挂了，这个告警系统就无法告警了么？
18. 写 Helm 时候如果要添加一个已有的 Chart，比如 MySql，如何直接调用或者添加呢？如果能用 Juju 展示出来更佳。详见 Helm Monocular 条目
