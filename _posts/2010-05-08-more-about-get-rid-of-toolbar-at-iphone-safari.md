---
title: More about get rid of toolbar at iPhone Safari
author: Fan Fan
layout: post
permalink: /2010/05/08/more-about-get-rid-of-toolbar-at-iphone-safari/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 604916036
categories:
  - Mobile
tags:
  - iphone
---
When you visit some web site like Google Reader or Delicious at iPhone Safari, maybe you will notice that after have loaded page, the browser will hide tool bar automatically. This feature can save phone&#8217;s valuable screen space. To implement it we can just use javascript: *window.scrollTo(0,1)*. See [here][1], and [here][2].

<pre class="brush:javascript">&lt;script type="application/x-javascript"&gt;

if (navigator.userAgent.indexOf('iPhone') != -1) {
 addEventListener("load", function() {
 setTimeout(hideURLbar, 0);
 }, false);
}

function hideURLbar() {
 window.scrollTo(0, 1);
}

&lt;/script&gt;</pre>

In common sense, it works. But if it didn&#8217;t, this article will help you.

First if we want toolbar hidden, beside the above JavaScript, below two conditions should be true:

1) **web page is long enough**. If it is short, the browser will think it is not necessary to hide the bar. You can zoom the page to test this rule.

2) ***scrollTo after page updated. ****setTimeout* is used for this. We often invoke an AJAX to get HTML text from server and then update some part of page. The updating action will reload page so *window.scrollTo* must be invoked **after** AJAX has gotten response and page has been updated. So the timeout of scrollTo should be bigger than timeout of Ajax. Or invoke scrollTo after had gotten response directly. Keep in mind that setTimeout and Ajax both are **asynchronous**.  <span style="text-decoration: line-through;">I used jQuery and <em>$(#id).html(&#8220;<div>&#8230;&#8221;);</em> to update page, so for DRY, </span><span style="text-decoration: line-through;">I override this function</span>:

<pre class="brush:javascript">if (navigator.userAgent.indexOf('iPhone') != -1) {
 (function ($) {
 var originalVal = $.fn.html;
 var hideURLbar = function() {window.scrollTo(0,1);};
 $.fn.html = function(value) {
var ret = originalVal.call(this, value);
 if (typeof value != 'undefined') {
 // setter invoked, do processing
 setTimeout(hideURLbar, 100);
 }
 return ret;
 };
 })(jQuery);
};</pre>

I found this cool script from [here][3]. Mabye this approach impacts app much, for html method is used everywhere in app. So take care before apply it. There is another option: use* $(document).trigger* to sent event.  
<img class="alignnone size-full wp-image-745" title="Screenshot 2010.05.08 14.39.26" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/05/Screenshot-2010.05.08-14.39.26.png" alt="Screenshot 2010.05.08 14.39.26" width="320" height="480" />

 [1]: http://ajaxian.com/archives/iphone-web-development-tips-and-official-documentation-released
 [2]: http://www.iphonewebdev.com/examples/
 [3]: http://stackoverflow.com/questions/2059907/override-jquery-val-function