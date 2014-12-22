---
title: Cygwin tips
author: Fan Fan
layout: post
permalink: /2007/06/19/cygwin-tips/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/06/cygwin-tips.html
categories:
  - UNIX
---
1.if the os is windows, the file type will be dos by default. So if you new a shell script, then run it in cygwin, the error like &#8220;who/r command not found&#8221; will happen. The solution is changing the file type to unix. A approch is using vi. In vi, use &#8220;:set ff?&#8221; to inquiry file type. Then use &#8220;:set fileformat=unix&#8221; or &#8220;:set ff=unix&#8221;.

2.In my machine, cygwin.bat is:bash &#8211;login -i. This will let shell be a login shell. But only an interactive shell that is not a login shell will read and execute ~/.bashrc. If we want our personal bash, we should create a .profile file in home directory instead of of .bashrc. One example is:

alias h=&#8221;history&#8221;  
alias hg=&#8221;h|grep&#8221; 

#alias ls=&#8217;ls &#8211;color=auto&#8217;  
alias la=&#8217;ls -A&#8217;  
alias lf=&#8217;ls -F&#8217;  
alias ll=&#8217;ls -l&#8217; 

&nbsp;3.use wget with proxy. create a .wgetrc in home directory.

http_proxy = proxy.com:81  
no_proxy = localhost 

This will make wget to fetch data with proxy by default and you didn&#8217;t need to swith to proxy model by &#8221; -Y on&#8221;.