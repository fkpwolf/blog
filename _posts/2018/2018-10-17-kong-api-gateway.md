---
layout: post
title:  "Kong as an API Gateway"
date:   2018-10-17 14:20:33
categories:
  - cloud
typora-root-url: ../../../blog
---

### 安装

    helm install stable/kong

会创建两个 service：一个 admin，一个 proxy。 

<https://192.168.51.11:30291/> 这个不能用 k8s-1 hostname，为什么？页面啥没有，只返回一个 json。 

<https://docs.konghq.com/0.14.x/getting-started/configuring-a-service/> 这里创建一个 hello world 的接口： 

    curl -i -X POST --url <https://k8s-1:30291/services/> --data 'name=example-service' --data 'url=[http://mockbin.org](http://mockbin.org/)' -k 
    curl -i -X POST --url <https://k8s-1:30291/services/example-service/routes> --data 'hosts[]=[example.com](http://example.com/)' -k 

第一条命令创建 service，这里用一个已经存在的 mockbin 作为测试服务，第二条创建路由。 

    curl -i -X GET --url https://k8s-1:31831 --header 'Host: [example.com](http://example.com/)' -k 

然后访问。前面用的都是 admin 端口，这里要用 proxy 端口。返回是网站 HTML，这里修改 mockbin REST 地址，返回一个更有意义的。不过现在 mockbin 已经不能创建新的条目了，说是 Redis 满了 <https://github.com/Kong/mockbin/issues/78> 原来这个网站是开源的，和 Kong 同一作者。

    curl -i -X POST --url https://k8s-1:30291/services/ --data 'name=example-service' --data 'url=http://mockbin.com/request?foo=bar' -k 

返回 HTTP Header 里面加了 Via: kong/0.14.0 

<http://mockbin.com/request> 这个可以网站打开 web ui，显示请求的 Web HTTP Header，代码访问设置 Accept Header 返回 json，不错的设计。 

这一条条命令太麻烦，有 web ui：<https://github.com/pantsel/konga> <https://github.com/PGBI/kong-dashboard> 

试了下 <https://github.com/PGBI/kong-dashboard>，这个没有 helm，使用 k8s dashboard 安装，需要设置： 

    参数: start --kong-url [https://kilted-llama-kong-admin.kong:8444](https://kilted-llama-kong-admin.kong:8444/) 

Error: self signed certificate，已知 [issue](https://github.com/PGBI/kong-dashboard/issues/168)，暂时没办法，只能修改 helm chart。 helm inspect stable/kong 查看参数后， 

    helm install stable/kong --namespace kong --set admin.useTLS=false --set proxy.useTLS=false 

然后 k8s dashboard 这个部署显示错误： 

    Readiness probe failed: Get https://10.36.0.223:8444/status: http: server gave HTTP response to HTTPS client 

修改部署 yaml 里面 livenessProbe 和 readinessProbe，"scheme": "HTTP”改成这个。但是其服务内部端口还是 8444，虽然没啥关系，但是怪怪的。这个 chart bug 挺多。 

![kong-ui](/images/2018/kong-ui.png)

有了这个界面，创建 service, router 就容易多了啊！这个应该是内置的。Istio 也必须有这个，否则乱七八糟太复杂。

按照 [get started - enable plugin](https://docs.konghq.com/0.14.x/getting-started/enabling-plugins/)， 

    curl -i -X POST --url <http://k8s-1:32103/services/foobar/plugins/> --data 'name=key-auth’ 

可以成功。界面上可以方便的 disable。

![kong-ui-plugins](/images/2018/kong-ui-plugins.png)

一切一换，访问结果立马不同（能访问和需要认证），有点神奇。对于这种经常需要切换的就需要有这样的操作界面。 

从上图可以看到 plugin 能关联 kong 里面所有其他的资源：service/route, consumer。 

### 认证 Plugin

<https://github.com/nokia/kong-oidc> 这个 plugin 不是内置的，进入 pod 命令行，运行 luarocks install kong-oidc，这个命令 pod 已经包含。但是调用 admin api 创建时报错："config": "plugin 'oidc' not enabled; add it to the 'plugins' configuration property”。说明我上面安装的 plugin 没有生效？在 pod console 下 /etc/kong/kong.conf.default，加入一条 plugins = bundled, oidc，运行 kong reload，然后 api 就可以调用成功。 

上访问 router，这时会跳到登录 keycloak 登录页面。感觉差不多成功了。然后修改 client_id && client_secret，值都可以在 keycloak 管理页面找到。这时 keycloak 页面会报错：无效的参数 : redirect_uri。原来遇到过，还是 keycloak console 创建一个新的 client 吧，设置其 Valid Redirect URIs <http://k8s-1:32402/>* ，这里用了 url wildcard。然后访问http://k8s-1:32402/aaa，需要输入用户名密码。然后就可以访问后面的 API （http://mockbin.com/request ）了，刷新页面也不用重新登录，这是因为浏览器带了 cookie 了。http://mockbin.com/request 界面可以看到所有的请求参数，用来做调试用，里面确实带了一个编码后的 X-Userinfo 值。也有 session timeout 机制。 

感觉这个可以取代 k8s dashboard keycloak proxy了，原理页差不多，都是 cookie。不对，dashboard proxy 只是 cookie->jwt token，本身不做认证，而是 proxy 下面的 api server 来做认证。下面这个图很清楚，这里 kong oidc plugin 在没有登录时会到 keycloak 做认证并且缓存授权信息，登录后自己只校验 cookie。后面 API 总是不做认证。 

![kong-auth](/images/2018/kong-auth.png)

但是这个 cookie 方案只适合浏览器，对于代码怎么带 cooke 呢？只能从前端开始就带上了。 

而且这个方案要自己保存 cookie，整个变成一个有状态服务了。 

对于我们的需求：一个 dashboard+自己的 api，这个方案就没法适用了。而且我现在才发现：上面方案没有使用 JWT！ 

<https://docs.konghq.com/hub/kong-inc/jwt/> 这个是官方的 plugin，直接用证书校验，所以还不能和 keycloak 关联。 

感觉我需要的只是：使用 keycloak 证书认证 JWT，就如 k8s api server 所做的事情一样。 

参照上面步骤，其实都可以在 kong dashboard 中完成。 

![kong-jwt](/images/2018/kong-jwt.png)

现在开始测试：不带任何 header，返回： 

    401 "message": “Unauthorized" 

Postman 加上 Bearer token，随便填 

    401 "message": "Bad token; invalid JSON” 

好的，加上一个格式正确的： 

    "message": "No credentials found for given 'iss’” 

好的，创建一个有效的： 

![jwt](/images/2018/jwt.png)

上面 iss 是 JWT credentials key，verify signature 里面要填入 JWT credentials secret。 

整个串联流程是：创建 JWT Plugin，其挂上一个 Router，这样访问 Router 的时候都要先经过这些 plugin（像过滤器或者管道，切面，钩子）。JWT Plugin 校验 token 时会从 Consumers 里面找到对应的用户，如果找到用户并且签名正确（个人臆想），则请求可以从这个 plugin 可以顺利通过。 

![kong-consumers](/images/2018/kong-consumers.png)

Consumer 这里可以配置用户的方方面面，包含 group。这里演变成一个用户管理系统了？ 

为了避免每次访问 proxy 都用端口号，起一个新的 Ingress，host 匹配为：host: "*.proxy.kong.x.access.ly”。注意要用双引号才能 wildcard。这样 router 配置 host 为 aaa.proxy.kong.x.access.ly 就可以直接访问了并且也能和其他 router 很容易的区分开。 

Router 和 Service 定义有重复，似乎是在模仿 k8s ingress 和 service 概念。kong service 有些自己特性：可以设定重试次数，默认为 5 次，还可以设定 timeout 时间。 

总的来说，这个 Kong 挺轻量级，比 Istio 通过 yaml 来操作 CRD 简单多了。还有这个有个 UI，容易上手。虽然将来可能觉得 UI 不够强大灵活，但是对于新手 UI 帮助很大。入门后可以抛弃 UI 而用命令行来搞定，但并不意味着这个 UI 没有起到帮助。 

[架构](https://docs.konghq.com/enterprise/0.31-x/kong-architecture-overview/) 基于 Nginx，为了更动态和灵活配置，使用了 OpenResty。 
[这里](https://docs.konghq.com/enterprise/0.32-x/kong-architecture-patterns/)有谈到 Multi-datacenter with Cassandra，不知道是不是企业版专有。 

![kong-arch](/images/2018/kong-arch.png)

这个 api gateway，就有点像 service mesh 了。 

![](/images/2018/kong-old-vs-new.png)

这里面的 Kong 提供很多功能，比如认证就功能很多，号称 api 网关，使用了数据库，不知道这里为什么要这么复杂，为什么不用 etcd？ Kong 人气很旺，也是基于 OpenResty 开发，[选择Kong作为你的API网关](https://www.cnkirito.moe/kong-introduction/)。其 <https://github.com/Kong/kubernetes-ingress-controller> 基于 ingress nginx，版本才 0.1.1，这是专门搞企业版去了么？ 

虽然 nginx ingress 功能现在比较少，但是优势在于 nginx 非常成熟和经过考验，扩展多。 

