---
title: Android上的Push Notification和XMPP(1)
author: Fan Fan
layout: post
permalink: /2009/11/15/android%e4%b8%8a%e7%9a%84push-notification%e5%92%8cxmpp1/
dsq_thread_id:
  - 611477843
categories:
  - Mobile
---
Android有个让人赞赏的特征：实时的同步。

1）如果你有新的Gmail邮件，手机可以马上收到邮件通知，这个中间可能有2，3秒的延迟，一般感觉还是很及时的；

2）如果你的联系人和Google Contanct是关联的话，你用桌面浏览器访问Gmail，修改联系人信息，很快新的联系人信息就会同步到你手机上。

最简单的实现可能用轮询（Polling）了，但这种方式有缺点：一则增大服务器的负担，想想如果每个客户端每隔半小时访问服务器，而大多数情况下服务器是没有更新数据的，对比起来服务器的负担就很浪费了，半小时的时间又很难做到实时的更新&#8230;；二则对于手机这种客户端，轮询浪费电源这个手机最宝贵的资源。

解决的方式是长连接，有好几种，这里讲的比较全面：<a href="http://www.infoq.com/cn/news/2008/08/beyondpolling" target="_blank">超越轮询？考虑PubSub、Push和MOM</a>。也可用类似Comet的长连接，而Android里面用的是XMPP。

当运行了上面的第二种操作后，在logcat里面可以看到如下log：  
11-02 21:45:08.706: DEBUG/Smack/Packet(148): [READ] <message id=&#8221;xL62aX-26&#8243; stanza-id=&#8221;269508591457&#8243; to=&#8221;fkpwolf@gmail.com/androidfCv23kaFEJQd0Mc&#8221; from=&#8221;gmail.com&#8221; type=&#8221;headline&#8221;><data xmlns=&#8221;google:mobile:data&#8221; category=&#8221;GSYNC\_TICKLE&#8221; token=&#8221;http://www.google.com/m8/feeds/contacts/fkpwolf@gmail.com/base2\_property-android&#8221; from-trusted-server=&#8221;true&#8221;><app-data key=&#8221;account&#8221; value=&#8221;fkpwolf@gmail.com&#8221; /></data></message>

网上查找一番后会发现Smack是XMPP一个比较流行的客户端lib，而这里面的message正是XMPP一种类型为headline的消息，为通知类的消息，而聊天的则为chat类型。虽然Smack是开源的，Android里面的这部分做的很private，search代码没有任何发现。Google虽然不做恶，但是也不是做善事的。