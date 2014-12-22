---
title: continuation in Spring Web Flow
author: Fan Fan
layout: post
permalink: /2006/08/16/continuation-in-spring-web-flow/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/05/continuation-in-spring-web-flow.html
dsq_thread_id:
  - 621281533
categories:
  - Web Development
---
</p> 

&nbsp;&nbsp; 平心而论，SWF是现在java中对continuation支持最好的和最现实的，虽然这种支持是模拟的。其他的一些框架都有点技术上的理想而造成的不成熟。  
&nbsp;&nbsp;&nbsp; SWF默认使用的控制是SimpleFlowExecutionRepository，这是不支持continuation的，比如浏览器回退、新开浏览器等。如果要支持，要使用ContinuationFlowExecutionRepository。SWF这样的设置是很灵活的，因为一般的应用可以使用SimpleFlowExecutionRepository来避免continuation引起的不必要的开销。  
&nbsp;&nbsp;&nbsp; 以swf-sample中的guessnumber为例（guess number是一个经典的例子），修改/web-inf/dispatcher-servlet.xml相关内容如下：  
`&nbsp;&nbsp;&nbsp;&nbsp; <bean name="/play.htm" class="org.springframework.webflow.executor.mvc.FlowController"><br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <property name="flowExecutor" ref="flowExecutor"/><br />&nbsp;&nbsp;&nbsp;&nbsp; </bean><br />&nbsp;&nbsp;&nbsp; <bean id="flowExecutor" class="org.springframework.webflow.executor.FlowExecutorImpl"><br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <constructor-arg ref="repositoryFactory"/><br />&nbsp;&nbsp;&nbsp; </bean><br />&nbsp;&nbsp;&nbsp; <bean id="repositoryFactory" class="org.springframework.webflow.execution.repository.continuation.ContinuationFlowExecutionRepositoryFactory"><br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <constructor-arg ref="flowRegistry"/><br />&nbsp;&nbsp;&nbsp; </bean>&nbsp;&nbsp;&nbsp; <br />&nbsp;&nbsp;&nbsp; `如此后，应用就支持浏览器后退键了。这里的后退键是跟后台逻辑关联在一起的：按下后退键，相当于刚才的猜测不算，再猜一次，而计数也“后退”（或者说“恢复”）到前面到状态。如果是购物篮应用，后退就表示“退回”刚才购买的商品。（这种方式也可能造成用户感觉上的混乱）  
&nbsp;&nbsp;&nbsp; 这种功能用一般的会话变量（比如将计数存在session中、将购物篮存在session中）是很难处理的。这里的会话变量比如计数、购物篮是一个会话活动中多个步骤的结果的记录，用一个变量是无法记录这些步骤的先后顺序的。而一般的会话变量只是一个步骤中标志或者状态的记录，一个变量就足够使用。  
&nbsp;&nbsp;&nbsp; SWF的解决方法就是在每个步骤中添加一个标志，当这个步骤提交时，后台根据提交上来的标志恢复（在回退的情况下）当时这个步骤的上下文，这样就可以在work flow中继续运行下去。  
&nbsp;&nbsp;&nbsp; 例如当步骤1->2->3->4，当浏览器在步骤4时回退时，浏览器会把步骤2提交的数据再次提交。  
&nbsp;&nbsp;&nbsp; 用户每次提交，后台都要形成一个上下文的continuation（每次提交的数据都可能不一样），这可能会占用大量的资源。