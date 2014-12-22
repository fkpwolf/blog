---
title: jdk6 has been released!
author: Fan Fan
layout: post
permalink: /2006/12/16/jdk6-has-been-released/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/06/jdk6-has-been-released.html
categories:
  - NT
---
Tonight I occasionally browsed java.sun.com, It was very surprising that J2SE 6 had been available. COOL! Well Done! It was so quickly that sun pushed the newer edition of jdk.  
After going through [this page][1], I found there are not too much exciting features in this release except script.  
More detail documents can be found in DevX: [Mustang Must-Haves: What&#8217;s Cool in Java SE 6][2] and[ Java SE 6&#8217;s New Scripting and Compiling Goodies][3] .  
You can see another cool stuff is compile API. Even there is a project [gosling][4] using compile API as a replacement for ANT. It used java as build language other than xml. Also it used some advanced feature such as annotation, compile API.  
The questions for gosling are :  
1) the build java file need not be compiled?  
2) Is it more quickly than old ANT?  
3) Why not use script as a language? Though the java maybe be a better choice than XML, it also has a more complex grammar than XML. With XSL XML can be validated easily. If the java don&#8217;t be compiled in eclipse, how can I make sure my java is correct? till the run-time? How about script? maybe it is more appropriate.

 [1]: http://java.sun.com/javase/6/
 [2]: http://www.devx.com/Java/Article/33398/0/page/1
 [3]: http://www.devx.com/Java/Article/33206/0/page/1
 [4]: https://gosling.dev.java.net/