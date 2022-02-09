---
layout: post
title:  "计算机安全整理"
date:   2017-7-19 14:20:33
typora-root-url: ../../../blog
---

<http://lynahex.com/index.php/archives/usb-attack.html> USB攻击。其会模拟一个键盘或者网卡，然后向这个键盘和网卡发送指令，比如模拟人的键盘输入。 

SSL/TLS协议运行机制的概述 <http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html> 

RSA算法原理（一） <http://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html> 

HTTPS 图解 <https://tls.ulfheim.net/> 

深入理解跨站点 WebSocket 劫持漏洞的原理及防范 <https://www.ibm.com/developerworks/cn/java/j-lo-websocket-cross-site/> 

浏览器同源政策及其规避方法 <http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html> 

详解JS跨域问题 <https://github.com/wangning0/Autumn_Ning_Blog/blob/master/blogs/10-16/js_co.md> 

小米路由器劫持用户浏览器事件回顾 <http://drops.wooyun.org/tips/6820> 

如何用通俗语言解释什么是量子通信？<https://www.zhihu.com/question/20919153> 

给你的web应用上MFA(Multi-Factor Authentication) <https://www.jianshu.com/p/26f1974f60da>

数字签名是什么？ <http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html> 

服务器用私钥加密，客户用公钥解密，这样用公钥就可以确认信息确实由某人发出 

![encode](/images/2017/encode.png)

![decode](/images/2017/decode.png)

第一步是对邮件里面的附件签名进行解密，得到摘要 digest。然后对整个邮件进行 hash（哪种哈希算法？），得到新的摘要，对比两个摘要来判断是否确实由某人发出。 

ssh-copy-id 是常用的免密登录方式，这个命令会把本地计算机的公钥拷贝到远程 ssh 服务器的 `.ssh/authorized_keys` 中，格式为 `ssh-rsa AAAAB3Ng...Jxr fan@fans-iMac.lan` 登录时需要使用密钥，密钥文件开始为 `-----BEGIN OPENSSH PRIVATE KEY-----`。

<https://blog.cloudflare.com/memcrashed-major-amplification-attacks-from-port-11211/> Memcached 默认使用了 UDP，而且没有绑定在 127.0.0.1，也没有在防火墙后面。15 个字节的请求返回 134KB，这个放大了一万倍。这个就是放大攻击。TCP 不能放大么？可能 TCP 需要保持连接，对客户端要求更严格了。最后建议开发者不要使用 UDP。 

[QQ 为什么以 UDP 协议为主，以 TCP 协议为辅？](https://www.zhihu.com/question/20292749) “原因是因为当时没有epoll这种可以支持成千上万tcp并发连接的技术,所以他们使用了udp,然后在udp上面封装了一下,模拟了一下tcp,解决了大并发的问题,之后因为做的很nb了,虽然epoll这种技术出现了,还是没有改回使用tcp了.现在再做类似的东西就不需要使用udp了.” 

[https://www.cloudflare.com](https://www.cloudflare.com/) 业内顶尖的网络安全有性能优化公司 

<https://www.cyberciti.biz/faq/understanding-etcshadow-file/>

/etc/shadow

![etc shadow](/images/2017/etc-shadow.png)

第二部分就是密码，格式为`Usually password format is set to $id$salt$hashed`。`$6$ is SHA-512` 

『查表和彩虹表的方式之所以有效是因为每一个密码的都是通过同样的方式来进行hash的。如果两个用户使用了同样的密码，那么一定他们的密码hash也一定相同。我们可以通过让每一个hash随机化，同一个密码hash两次，得到的不同的hash来避免这种攻击。具体的操作就是给密码加一个随即的前缀或者后缀，然后再进行hash。这个随即的后缀或者前缀成为“盐”。正如上面给出的例子一样，通过加盐，相同的密码每次hash都是完全不一样的字符串了。检查用户输入的密码是否正确的时候，我们也还需要这个盐，所以盐一般都是跟hash一起保存在数据库里，或者作为hash字符串的一部分。』 但是我验证了下，通过命令行生成 hash，和上面比照不一样，可能还有其他的安全措施。

和我们自定义的加salt算法不同，Hmac算法针对所有哈希算法都通用，无论是MD5还是SHA-1。采用Hmac替代我们自己的salt算法，可以使程序算法更标准化，也更安全。Linux 下可以用命令 `echo -n "value" | openssl dgst -sha1 -hmac “key"` 来获取加密信息。

https://sha256algorithm.com/ SHA256算法的动画演示

哈希碰撞与生日攻击 <http://www.ruanyifeng.com/blog/2018/09/hash-collision-and-birthday-attack.html> 

CSRF 跨站请求伪造 session riding，用户登录到 A 后，用户访问 B 网站，B 网站上面直接访问 <img src="http://www.A.com/withdraw?account=Alice&amount=1000&for=Badman">，因为 A session 未过期，所以这个 Img 能直接伪造请求。

解决办法：检查Referer字。或者添加校验token。或者像 JWT 那样，token 直接放到 HTTP header 里面。

XSS 跨站脚本，是指在A 网站上面直接运行脚本。

解决办法：将用户所提供的内容进行过滤。

XSS 利用的是用户对指定网站的信任（比如论坛网站，用户信任它，所以 XSS 的责任全在论坛网站），CSRF 利用的是网站对用户网页浏览器的信任(用户并不信任 B 网站，B 网站是黑客站点，但是A 网站信任浏览器发送的请求)。 

证书的生成命令[举例](https://github.com/kubernetes/dashboard/issues/2954#issuecomment-385354244)：
```
openssl req -nodes -newkey rsa:2048 -keyout certs/dashboard.key -out certs/dashboard.csr -subj "/C=/ST=/L=/O=/OU=/CN=kubernetes-dashboard"
openssl x509 -req -sha256 -days 365 -in certs/dashboard.csr -signkey certs/dashboard.key -out certs/dashboard.crt
```
这就产生了标准的三个证书文件。
