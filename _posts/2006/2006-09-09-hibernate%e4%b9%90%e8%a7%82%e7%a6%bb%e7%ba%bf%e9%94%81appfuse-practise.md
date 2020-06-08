---
title: Hibernate乐观离线锁(Appfuse Practise)
author: Fan Fan
layout: post
permalink: /2006/09/09/hibernate%e4%b9%90%e8%a7%82%e7%a6%bb%e7%ba%bf%e9%94%81appfuse-practise/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/06/hibernateappfuse-practise.html
dsq_thread_id:
  - 617171732
categories:
  - Hibernate
---
<a onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}" href="http://1.bp.blogspot.com/_lz-dDaxCZHc/Rmaqz3h5QYI/AAAAAAAAABs/jRfSRI2E5yY/s1600-h/01695366102a6624ab184cb5.jpg.bmp"><img style="margin: 0px auto 10px; display: block; text-align: center; cursor: pointer;" src="http://1.bp.blogspot.com/_lz-dDaxCZHc/Rmaqz3h5QYI/AAAAAAAAABs/jRfSRI2E5yY/s400/01695366102a6624ab184cb5.jpg.bmp" alt="" id="BLOGGER_PHOTO_ID_5072929838335607170" border="0" /></a>  
一个在Hibernate中的实践操作描述的很好的文档在[这里][1]。原理上的描述在《Addison Wesley : Patterns of Enterprise Application Architecture》 Chapter 16里面有详尽的描写，这本 Martin Fowler 撰写的书称乐观锁定为Optimistic Offline Lock，Hibernate的实现基本上跟这本书里面的一样。里面有副时序图，画的相当的明白。(copy from the book. copyleft.)

另外一本书Hibernate in Action将乐观锁划分为Application Transacation，而悲观锁则划分为Database Transaction（Hibernate总是使用数据库的锁定机制，从不在内存中锁定对象！见：Hibernate的reference document）。  

### Appfuse实践  

Appfuse可以generate一个model的CRUD所有jsf页面，这可以作为实践上的起步。然后，version字段应该在页面上不能修改，而是作为一个hidden的字段，这样在显示record详细信息界面上就需要有两个hidden字段：  
```html
<h:inputHidden value="#{personForm.person.id}" id="id"/>
<h:inputHidden value="#{personForm.person.version}" id="version"/> 
```
Patterns of Enterprise Application Architecture里面的做法是将version作为session变量，都是可以的。只要这个值是读取对象时取出来的就可以。  
这样做后，如果两个不同的用户同时修改一条记录时，hibernate就会报错Row was updated or deleted by another transaction。

 [1]: http://caterpillar.onlyfun.net/Gossip/HibernateGossip/OptimisticLocking.html
