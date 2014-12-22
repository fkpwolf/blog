---
title: The unique of Tapestry
author: Fan Fan
layout: post
permalink: /2007/09/12/the-unique-of-tapestry/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/09/unique-of-tapestry.html
categories:
  - Web Development
---
&nbsp; What&#8217;s the difference between web development and traditional C/S application such Swing? Personally I like Swing because it let me more efficient. All the code is Java code and there is little type-cast. And Tapestry also follows this way. The author admitted [that][1]:  
  


<blockquote style="font-style: italic">
  An advantage for Tapestry, I think, is the way the two pages (the firsts containing the form, the second displaying the result) communicate &#8230; in proper, type-safe Java code.</p>
</blockquote>

&nbsp; This means if you want to invoke a web page, what you face is a java class. Blow is a example:  
`public IPage doSubmit()<br />{<br />ShowProject showProject = getShowProject();<br />showProject.setProject(getProject());<br />return showProject;<br />}`  
&nbsp; Get a page class(by injection), give it some value and then you can forward to that page. The page class has included a page and we can say it is the interface of the page. But the best way, I think, is the function like way. Some likes &#8220;showProject(project);&#8221;. Compared with Object initialization which is not undocumented and not self-explaining, function maybe is more intuitive.

&nbsp; Tapestry just resolved the problem of the two pages communicate. As to multi-page, or a conversation, there are a lot of work. For example, how to get a page&#8217;s return value?

&nbsp; Unlike this way that page and bean are bound together, JSF let page outside the backend bean. And a jsf page can bind multi-bean. A backend bean can be used in many jsf pages. This loose bind let a page include another page easily.

 [1]: http://tapestryjava.blogspot.com/2005/02/tapestry-jsf-and-fud.html