---
title: View Google Reader in Android Browser through Yahoo Pipes
author: Fan Fan
layout: post
permalink: /2009/05/27/view-google-reader-in-android-browser-through-yahoo-pipes/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2009/05/view-google-reader-in-android-browser.html
categories:
  - Mobile
---
I am fan of Google Reader. So when I got a Android G1 device, soon I visited the site through it. Now I can view blogs when I was on the traffic to office. It is cool. But one problem I encountered is the font size is too small to read comfortably. I have tried to set the font size to &#8220;large&#8221; in browser setting and it works at some common page like www.google.com but not Reader. It looks that Google Reder did some optimize for mobile device(It designed for iPhone at first) and the browser can&#8217;t adjust itself on font size.

<div style="TEXT-ALIGN: left; DISPLAY: block">
  <div style="TEXT-ALIGN: left; DISPLAY: block">
    <img class="aligncenter" title="old" src="http://sites.google.com/site/knowledge/google-android/view-google-reader-in-android-browser-through-yahoo-pipes/a.png" alt="" width="575" height="385" />
  </div>
  
  <p>
    I think I just to want to make the main content&#8217;s font of blog item bigger.
  </p>
  
  <p>
    Greasemonkey is power tool to customize web page. I tried <a href="http://jsharkey.org/blog/2008/12/15/oilcan-greasemonkey-on-steroids-for-android/">OilCan</a> &#8211; Greasymonkey on Android browser. It has an innovative idea but still at beta phase. Google chromium has supported it but at Android chrome lite should not support it at now.
  </p>
  
  <p>
    Yahoo Pipes fits this case perfectly. Look below pipe flow:
  </p>
</div>

<div style="TEXT-ALIGN: left; DISPLAY: block">
  <div style="TEXT-ALIGN: left; DISPLAY: block">
    <img class="aligncenter" title="pipes" src="http://sites.google.com/site/knowledge/google-android/view-google-reader-in-android-browser-through-yahoo-pipes/pipes.png" alt="" width="614" height="443" />
  </div>
  
  <p>
    It was simple: just surround the blog content with a font size modification. Then it works. You can view this pipe from <a href="http://pipes.yahoo.com/pipes/pipe.info?_id=rKg_iGhA3hGoK9dqBR50VA">here</a>. Thanks Yahoo to create this great tool. The web UI is impressed and intuitive for building a flow. All operations can be finished by drag & drop or drop-box.
  </p>
  
  <p>
    More on this, Pipes can be seen as cloud computing. It is running at Yahoo&#8217;s server. Different with Google App Engine which is sophisticated and for programmer, it is for general user who does not need to have programming experience. Of course it has less function then full feature language. Its input just accepts RSS and others feeds defined in advance. The more inputs type, the more powerful Pipes becomes.
  </p>
</div>