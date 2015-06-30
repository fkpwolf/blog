---
title: upgrade to Ubuntu 10.10
author: Fan Fan
layout: post
permalink: /2010/08/14/upgrade-to-ubuntu-10-10/
aktt_notify_twitter:
  - no
  - no
dsq_thread_id:
  - 614401375
categories:
  - UNIX
---
There is a lot of time so I upgraded my Ubuntu to 10.10 even though it didn&#8217;t come to last release.

The biggest change is now it has a new theme: Ambiance-maverick-beta. Looks nice. [Some][1] called it &#8220;Light theme&#8221;.

And the nvidia driver looks not work with new kernel. You have edit /etc/X11/x.conf to change &#8220;nvidia&#8221; to &#8220;nv&#8221;, then you can login into X window, otherwise you will get a &#8220;no screen found&#8221; or &#8220;can&#8217;t load module nvidia&#8221; exception.

Freetype has been upgraded to version 2.4. This version turn on &#8220;Turetype bytecode&#8221; by default since this [patent][2] has expired. But I didn&#8217;t find big changes at font rendering of this version.

 [1]: http://design.canonical.com/2010/08/an-update-to-the-ubuntu-light-themes/
 [2]: http://freetype.sourceforge.net/patents.html