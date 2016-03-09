
An h1 header
============

Paragraphs are separated by a blank line.

2nd paragraph. *Italic*, **bold**, and `monospace`. Itemized lists
look like:

  * this one
  * that one
  * the other one

Note that --- not considering the `asterisk` --- the actual text
content starts at 4-columns in.

> Block quotes are
> written like so.
>
> They can span multiple paragraphs,
> if you like.

Use 3 dashes for an em-dash. Use 2 dashes for ranges (ex., "it's all
in chapters 12--14"). Three dots ... will be converted to an ellipsis.
Unicode is supported. ☺


### 花生壳
先按照这个配置 http://iloss.me/post/fen-xiang/2014-01-10-openwrt-ddns
DD-wrt下面，得到Ip方法为"nvram get wan_ipaddr". 也可以使用dd-wrt内置的DDNS更方便。参考[这里](http://bbs.oray.com/thread-74508-1-1.html)。

运行里面的shell脚本，然后在openwrt里面的Network -> Firewall -> Port Forwards里面配置要暴露的资源。或者修改/etc/config/firewall文件：a [链接](http://wiki.openwrt.org/doc/howto/port.forwarding)
然后运行 /etc/init.d/firewall restart
公司网络访问延迟很短啊：
```
C:\Users\IBM_ADMIN>ping fkpwolf.wicp.net
```
Pinging fkpwolf.wicp.net [114.86.52.31] with 32 bytes of data:

Minimum = 36ms, Maximum = 43ms, Average = 38ms
TP-LINK Archer C7, BB, ping 163.com, average=5ms, but sometimes 200ms?

For new Openwrt, looks there is no no-ip.com any more. Use this for update url:
http://[USERNAME]:[PASSWORD]@dynupdate.no-ip.com/nic/update


