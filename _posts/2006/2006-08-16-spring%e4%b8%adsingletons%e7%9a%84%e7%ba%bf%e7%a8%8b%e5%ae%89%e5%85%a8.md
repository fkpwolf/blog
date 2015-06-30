---
title: Spring中Singletons的线程安全
author: Fan Fan
layout: post
permalink: /2006/08/16/spring%e4%b8%adsingletons%e7%9a%84%e7%ba%bf%e7%a8%8b%e5%ae%89%e5%85%a8/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/06/springsingletons.html
dsq_thread_id:
  - 619332508
categories:
  - Spring
---
Thread-safe在Java Concurrency in Practice这本书里面的定义是： 

A class is thread-safe if it behaves correctly when accessed from multiple threads, regardless of the scheduling or interleaving of the execution of those threads by the runtime environment, and with no additional synchronization or other coordination on the part of the calling code. 

Spring本人不是很熟悉，网上Google了下相关方面的话题，得到的有用的结果很少。可以参考的是：[Thread safety, singletons and Spring。][1]还有一篇是：[Thread-safe webapps using Spring][2]。  
基本上，Spring的thread-safe是其API自身的thread-safe。比如一个常见的场景（from appfuse）：  
public class UserManagerImpl extends BaseManager implements UserManager {  
private UserDao dao;  
&#8230;&#8230;  
public class UserDaoHibernate extends BaseDaoHibernate implements UserDao, UserDetailsService {  
&#8230;&#8230;  
public class BaseDaoHibernate extends HibernateDaoSupport implements Dao {  
这些bean都是Singletons的。  
一个类如果没有成员变量，那这个类肯定是thread-safe的，所以UserDaoHibernate的thread-safe取决于其父类。而 UserManagerImpl 的安全性又取决于UserDaoHibernate，最后是HibernateTemplate。可以看出，这里的bean都小心翼翼的维护其成员变量，或者基本没有成员变量，而将thread-safe转嫁给Spring的API。如果开发者按照约定的或者用自动产生的工具（appgen不错）来编写数据访问层，是没有线程安全性的问题的。Spring本身不提供这方面的保证。  
或者bean的定义为Singletons=&#8221;false&#8221;，也可以参考前面的一篇文章[Thread safety, singletons and Spring][1]，用lookup-method。<pro spring> charpter 5介绍的更详细：  
Lookup Method Injection was added to Spring to overcome the problems encountered when a bean depends on another bean with a different lifecycle—specifically, when a singleton depends on a non-singleton. In this situation, both setter and constructor injection result in the singleton maintaining a single instance of what should be a non-singleton bean. In some cases, you will want to have the singleton bean obtain a new instance of the non-singleton every time it requires the bean in question.  
显然，如果A(Singletons) depends B(Propotype)，使用这种方式可以避免A对B的访问并发和争用的问题。  
<pro spring>这本书（强于spring in action，后者感觉是本reference book）也对Singletons＝“true/false&#8221;的选择做了个小结：  
使用Singletons的情况有：  
1.Shared objects with no state;  
2.Shared object with read-only state;  
3.Shared object with shared state;  
4.High throughput objects with writable state. (synchronizing is need)  
使用propotype的情况有：  
1.Objects with writable stat;  
2.Objects with private state.  
与Spring的高度灵活不同，EJB的规范将同步作为一个服务（one of primary services），开发者开编写bean时不必考虑（也不能）线程相关的问题。session bean其分为两类，也有同步上的考虑。  
虽然thread-safe的问题总是存在，EJB也没有从本质上解决这个问题，但是其提出了这个问题，并给出了规范。

 [1]: http://erik.jteam.nl/?p=4
 [2]: http://www.javalobby.org/articles/thread-safe/index.jsp