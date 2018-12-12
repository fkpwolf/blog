---
layout: post
title:  "Web Development"
date:   2014-4-24 15:43:00
---
### 概述
- The Web platform: Browser technologies https://platform.html5.org/ 
- Google's dev site: https://developers.google.com/web/ 
- WebFundamentals: https://developers.google.com/web/fundamentals/ 

可用性（Usability）、可访问性（Accessibility）和可维护性（Maintainability） 

Solid introductory article on how web browsers work!
https://hacks.mozilla.org/2017/05/quantum-up-close-what-is-a-browser-engine/ 
Concurrent marking in V8 https://v8.dev/blog/concurrent-marking

https://www.w3.org/
The Design of HTML5 https://adactio.com/articles/1704，翻译 [HTML5 设计原理](https://github.com/ustccjw/Blog/issues/12)。
HTML Design Principles https://www.w3.org/TR/html-design-principles/
个人总结大概为向下兼容、实用大于完美。
如何理解 Web 语义化？ https://www.zhihu.com/question/20455165 简单说来就是让机器可以读懂内容。

### 兼容性
http://tongji.baidu.com/data/browser 浏览器市场份额
browser compatibility http://caniuse.com/#feat=flexbox 
http://windows.microsoft.com/zh-cn/internet-explorer/which-version-am-i-using
https://docs.angularjs.org/guide/ie 
https://jquery.com/browser-support/
http://bootstrapdocs.com/ http://getbootstrap.com/getting-started/#support (Bootstrap 3)
VM for different IE https://dev.windows.com/en-us/microsoft-edge/tools/vms/windows/ 
http://abbett.org/post/a-guide-to-building-webapps-with-ie8-support it use backbone.js.

中文字体 http://www.ruanyifeng.com/blog/2014/07/chinese_fonts.html

### Boilerplate - most from WebStrom
* Web Starter Kit - 用了Material Design Lite，这也是google自己的。responsive做的相当全。号称只用CSS，其实还是有JS，而且导致HTML巨长的CSS class name，感觉还没Angular Meterial做的干净。material.min.js虽然小，怎么和主流的侵入式JS框架配合呢？就搞一DEMO或者宣传站点？工具集还算经典: gulp,es5,reload.
* Bootstrap
* React Starter Kit - 有单独的topic
* HTML5 Boilerplate
* https://github.com/facebookincubator/create-react-app Facebook官方

### Framework
- Angular.js https://angularjs.org/ has on-fly example
How it works https://docs.angularjs.org/guide/concepts
http://stackoverflow.com/questions/9682092/databinding-in-angularjs
- https://github.com/tkrugg/angular-delite
基于AngularJS的企业软件前端架构
http://www.infoq.com/cn/presentations/frontend-architecture-based-on-angularjs
- underscore: http://underscorejs.org/  useful functional programming helpers
Backbone VS. AngularJS http://www.zhihu.com/question/21170137 
聊聊主流框架，Less/Sass/Compass/Bootstrap/H5bp https://ruby-china.org/topics/4370 
Backbone.js入门教程第二版  https://github.com/the5fire/backbonejs-learning-note 
样例 http://todomvc.com/ https://html5boilerplate.com/ 

JSONP http://www.ibm.com/developerworks/cn/web/wa-aj-jsonp1/ 这个对服务器有要求：需要接受callback参数，返回的数据要用这个callback function封装json。这个方案缺点在于无法获得调用的错误信息。

new fetch for AJAX http://updates.html5rocks.com/2015/03/introduction-to-fetch 
whatwg-fetch https://github.com/github/fetch window.fetch polyfill. cdap 用的是这个。
浏览器跨域方法与基于Fetch的Web请求最佳实践 https://segmentfault.com/a/1190000006095018

LightningJS is a safe, fast, and asynchronous embed code intended for third-party Javascript providers. Safe: ensures zero Javascript code conflicts. Fast: avoids blocking window.onload and document.ready.

MVC，MVP 和 MVVM 的图示 http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html

### Library
https://github.com/cameronmcefee/plax github 404 page
http://jtblin.github.io/angular-chart.js/ 对于图标，需要双向绑定么？
http://github.hubspot.com/odometer/api/themes/ number increment animation. For pure javascript, this is good one http://codepen.io/niklas-r/pen/HsjEv

WebP 探寻之路 https://isux.tencent.com/introduction-of-webp.html 性能比png更高

### UI库
- http://element.eleme.io/#/zh-CN Element-UI 1.0.7 发布了，Element-UI 是由饿了么公司前端团队开源，是一套为开发者、设计师和产品经理准备的基于 Vue 2.0 的组件库，提供了配套设计资源，帮助你的网站快速成型。
- http://fezvrasta.github.io/bootstrap-material-design/ Material Design for Bootstrap is a theme for Bootstrap 3 which lets you use the new Google Material Design in your favorite front-end framework. 需要 Javascript。
- http://www.layui.com/ 自己的 js 库
- http://semantic-ui.com/ JS库和 Bootstrap 一样是 jQuery，但是有官方的 React 集成
- https://react.semantic-ui.com/introduction
- http://purecss.io/
- https://ant.design/index-cn 蚂蚁金服的

### Tools
- REST mock https://github.com/gstroup/apimocker
- REST post ui: Postman, it can save rest request for reuse.

online code http://jsfiddle.net/ http://codepen.io/pen 
https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/todos 这个是最简单的，不过可以在沙盒上面直接玩，体验还不错。这个沙盒可以直接浏览器运行 vs code，也能保存代码到 github。You can deploy a production version of your sandbox using ZEIT Now. 大而全。

cdn http://cdn.code.baidu.com/
360’s http://libs.useso.com/
http://openapi.baidu.com/wiki/index.php?title=docs/cplat/libs 
http://qydev.weixin.qq.com/cdn/cdnjs.html 长命怎么没几个呢？

https://facebook.github.io/origami/  Origami is a free tool for designing modern user interfaces. Quickly put together a prototype, run it on your iPhone or iPad, iterate on it, and export code snippets your engineers can use.

resizer http://design.google.com/resizer/

WebView 下的调试 http://www.oschina.net/p/eruda

Use `gulp —tasks` to list all tasks.

https://www.mockapi.io/ 这个可以作为假的 api 云地址
http://rap2.taobao.org/ 这个特么的每个api rul 里面带 GET POST，可能为了方便页面测试，但是程序中怎么办？

BrowserSync https://scotch.io/@tgreco/how-to-use-browsersync-for-faster-development?utm_content=buffer2edde&utm_medium=social&utm_source=twitter.com&utm_campaign=buffer 这个可以同步多个浏览器

[phantomJs之殇，chrome-headless之生](https://insights.thoughtworks.cn/phantomjs-and-chrome-headless/), Puppeteer 测试用

WebAssembly
https://github.com/golang/go/wiki/WebAssembly
如何调用操作系统接口比如线程呢？
我甚至看到有 JavaScript 写的虚拟机，可以直接加载运行 Windows 2000，简单的说就是用 JS 模拟 x86 指令的运行，这这里的 WebAssembly 没有关系。

### Performance

SPDY 是什么？如何部署 SPDY？ <http://www.geekpark.net/topics/158198>  

SPDY also is part of HTTP/2 <http://zh.wikipedia.org/wiki/HTTP/2> 

Netty系列之Netty高性能之道  <http://www.infoq.com/cn/articles/netty-high-performance> 

提高 web 应用性能之 JavaScript 性能调优 <https://www.ibm.com/developerworks/cn/web/1107_zhouxiang_tunejs/> 

可以先创建和组装好了DOM之后再放入body，这样减少重画。 

提高 web 应用性能之 CSS 性能调优 <https://www.ibm.com/developerworks/cn/web/1109_zhouxiang_optcss/> 

ETag <https://zh.wikipedia.org/wiki/HTTP_ETag> 代表资源是否修改过的 hash 值 

    If-Modified-Since:Wed, 10 May 2017 07:58:51 GMT 
    If-None-Match:W/"102600-15bf15e2678” 

这两个字段都会发送，如果带 tag 的话对比会更为准确。 

cowboy-高性能简洁的erlang版web框架 <http://blog.yufeng.info/archives/3051>mokcAPI 是用这个搞的。好像只是 Express？ 

"在c实现中nginx是做的最好的，但是如果你用nginx写自己的业务代码的时候，你就会把整个系统的性能拖到实现者的水平，总体来讲性能不会太高。而基于cowboy的实现再加上erlang天然的优势，通常一台机器做到几万的QPS的业务，对实现者的要求非常低，可以大大促进生产力，额外的福利还有热升级和稳定可靠。” 

<https://github.com/heroku/cowboyku> Heroku 也在用做路由和转发，这点倒和 Express 很像，对于不同的 domain/url 路由到对应的应用，这是利用了 Erlang 热部署或者路由的功能？但是很长时间没更新了。<https://github.com/ninenines/cowboy> 这个倒是一直在更新。 

AMP，来自 Google 的移动页面优化方案 <https://imququ.com/post/amp-project.html> 


### Think
* 测试
UI 很难或者无法测试
* 兼容性
UI开发的局限性，不可避免的要做hack，UI 本来就是现实世界的模拟，而非真实的逻辑
* 稳定性
而 UI 是在不断变化中，新的模式，新的设计，都会冲击现有的代码结构。这会导致后期的维护性非常差，跟不上变化。
* 分离
想法是好的，也要努力做到。如果新特性对页面整体没有改变，很好，怎么完技巧都可以。
* 全局变量
虽然现在 javascript 大家已经不用全局变量，但是 UI 的特点导致全局不可避免。比如 Angular Material 在 body 上设置了 position: relative; 会对整个系统照成影响。还有 footer, header，弹出消息。
很多情况是布局修改了，很多组件都错位了。因为布局是个全局变量。
系统的全局变量越少，则系统越容易改变。而 web 系统越容易改变，这个架构设计的也越好。

### 组件思想
复杂的组件，比如 auto-complete，直接拿来用，很好。其他的能重用么？如果组件满足不了要求，怎么办？自己有能力改么？很多办法后来都变成了 hack。组件库本身也有依赖和侵入性，用了某个库，刚开始时用库自带的控件，很爽。但是如果库不带某个控件，引入第三方，则存在了兼容性的问题，比如经常看到某个组件的 angular.js 版本。jQuery 之所以流行，就是因为其依赖少。在组件库不存在标准的情况下，依赖少就很难得了。把组件所有元素比如 css/js 都封装到一个控件中，这个是很多框架努力的方向，但其导致的复杂度也导致其只能在特定的环境中运行。从某种程度上说，这些组件都应该是浏览器自带的，最终也会被浏览器实现。

列出系统的 hack 和全局变量，单独放在一个地方。不是要马上改变，而是循序渐进的来改。

只做应该做的事情
比如屏幕的自适应，要体现自然，让浏览器去做事情，让元素自己变化，通过语义就可以体现不同的显示方式。如果参与过多，代码则很难维护，更难于变化。作为对真实事物的映射，不仅在代码上要用到语义的元素，比如<header>，更要在大的范围显示对真实事物的映射。比如如果一件事情能在 css 中完成，则最好不用 javascript。为什么？因为分工不同，更因为这样得到了更好的代码可读性。

web 开发的目的：数据展示和交互。web 开发和其他 UI 开发相比，要坚持其开放、简单的原则。开放：从最简单的代码查看，css 查看，到跟随变化，js 的弱类型检查。简单：只有简单才能适应变化，只有简单才能不拘泥于技术而跟随设计的脚步，这才是 UI 开发的目的。复杂是本地/原生开发的优势。



