---
title: Use JPA with Hibernate in Netbeans(Tomcat)
author: Fan Fan
layout: post
permalink: /2007/11/25/use-jpa-with-hibernate-in-netbeanstomcat/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/11/use-jpa-with-hibernate-in.html
dsq_thread_id:
  - 655811961
categories:
  - NetBeans
---
0. prerequisite  
&nbsp; Tomcat 6.0.14  
&nbsp; Netbeans 6 rc1  
&nbsp; Hibernate core 3.2.5GA  
&nbsp; Hibernate EntityManager 3.3.1GA

1. Add a new JPA library implement  
&nbsp; The default used TopLink. Change to Hibernate.  
[<img style="border-top-width: 0px; border-left-width: 0px; border-bottom-width: 0px; border-right-width: 0px" height="352" alt="Untitled" src="http://lh5.google.com/fkpwolf/R00RrvbBuYI/AAAAAAAAAFA/0usmYNZE89g/Untitled_thumb%5B1%5D" width="638" border="0" />][1] 

2. Adjust in JSF design  
The default data provider ObjectArrayDataProvider works not well with JSF design UI.

[<img style="border-right: 0px; border-top: 0px; border-left: 0px; border-bottom: 0px" height="316" alt="image" src="http://lh6.google.com/fkpwolf/R00l6_bBuaI/AAAAAAAAAFo/7-YsOZUCktU/image_thumb%5B2%5D" width="444" border="0" />][2] 

&nbsp; My target is when let a jsf table &#8220;Bind to Data&#8221;, the all fields of a Hibernate JOPO can be chosen as the&nbsp; above dialog. These displayed fields come from provider&#8217;s <font face="Courier New">getFieldKeys</font> method which is part of interface <font face="Courier New">DataProvider</font> signature. So the simplest way is to extends ObjectArrayDataProvider and overide that method. Or just use <font face="Courier New">setObjectType(Student.class)</font>. Be sure to **compile** that class manually so the JSF Design can take this change into account.

Why not use :

<font face="Courier New">private ObjectArrayDataProvider objectArrayDataProvider1 = new ObjectArrayDataProvider(someType);</font> 

It does not work. Even I put them in _init() method of backend bean, as the CachedRowSet provider does. 

3. Done.

 [1]: http://lh4.google.com/fkpwolf/R00RpfbBuXI/AAAAAAAAAE4/id9zp2llrzM/Untitled%5B3%5D
 [2]: http://lh5.google.com/fkpwolf/R00l5vbBuZI/AAAAAAAAAFg/o8W-DSN7UUU/image%5B6%5D