---
title: Cairo under Mac os X (Quartz)
author: Fan Fan
layout: post
permalink: /2009/08/02/cairo-under-mac-os-x-quartz/
dsq_thread_id:
  - 627216588
categories:
  - Graph
---
1) Install Cairo for Mac. You can install it by <a href="http://cairo.darwinports.com/" target="_blank">darwinports</a>. You should change the Portfile to enable cairo for configuration. The file located at /opt/local/var/macports/sources/rsync.macports.org/release/ports/graphics/cairo on my machine. Maybe there is another better way to do it, for example <a href="http://guide.macports.org/chunked/development.variants.html" target="_blank">Port Variants</a>.

2) check out the cairo demo code.  
`cvs -d :pserver:anoncvs@cvs.cairographics.org:/cvs/cairo co cairo-demo<br />
`  
3) Then there is a project named QuartzCaiorTest. Change the main.c to  
use the new API

<pre class="brush: c">//surface = cairo_quartz_surface_create(context, width, height);
surface =  cairo_quartz_surface_create_for_cg_context(context, width, height);</pre>

4) run &#8220;make&#8221; within project directory.

<img class="alignnone size-full wp-image-388" title="cairo" src="http://fkpwolf.net/WordPress/wp-content/uploads/2009/08/cairo.png" alt="cairo" width="253" height="243" />

The counterpart of Google on vector graphic library is Skia. It is also can built on Mac OS:https://skia.org/user/quick/macos.
