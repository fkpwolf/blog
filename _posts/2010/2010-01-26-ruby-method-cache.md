---
title: Ruby method cache
author: Fan Fan
layout: post
permalink: /2010/01/26/ruby-method-cache/
dsq_thread_id:
  - 615149360
categories:
  - Rails
tags:
  - cache
  - doubanclient
---
场景是这样的：我写的[手机客户端][1]要从豆瓣上取出当天最受欢迎的书评和影评，这些数据我是到douban.com相关页面上抓取的，大概有20条数据；然后我在这20条数据中随机取出6条显示到手机客户端

Rails内置[几种cache机制][2]：1.Page Cache &#8211; Fastest, 2.Action Cache &#8211; Next Fastest, 3.Fragment Cache &#8211; Least Fastest, 4.ActiveRecord Cache &#8211; Only available in Edge Rails 。从前端到后端，[这几种][3]cache机制粒度都不同，但是都不能满足我的需求。page cache会使每次返回同样的页面，这样达不到“随机”的效果。我希望是这样的：

<pre class="brush:ruby">def get_pop_list
  fetch_reviews.random
  render...
end

def fetch_reviews
  {1...20}
end</pre>

我只希望get_reviews这个方法返回的结果是cache的，Action cache好像符合，实验一番后无果，这里的action是指rails中的controller而不是一般的method。

google了下找到了[这里][4]，刚好满足我的需求：对method的cache。参考这里[例 子][5]:

<pre class="brush:ruby">require 'caches.rb'
  class Spider extend Caches
	def self.fetch_reviews
        	puts "---fetch_reviews"
	        Time.now
	end

	class_caches :fetch_reviews, :timeout =&gt; 10.minutes
  end</pre>

这里只是一个测试的类，当我们调用Spider.fetch_reviews时，得到的时间每次都是一样的，10分钟后值会刷新，有了timeout我也不用写cron程序来刷新cache了。这个method cache可以对参数cache：不同的参数值会有不同的cache，这个倒是很方便，因为这是一个静态方法，不可避免的要传参数。这点和Page Cache的分页处理倒类似。<span style="text-decoration: line-through;">一个要注意的是在development模式下，controller用load &#8220;spider&#8221;或者不显示引用（默认为load）不能达到cache的测试效果。</span>

用Java的单例模式来完成这个任务也不太难，这里的self.fetch\_reviews也是类的静态方法。不同的是ruby的做法让cache更加透明：加上或者去掉class\_caches :fetch_reviews即可改变cache模式，客户端代码不用改变。

 [1]: http://zudou.net
 [2]: http://www.javaeye.com/wiki/Rails-EveryDay/1160-Rails%20Cache
 [3]: http://www.ibm.com/developerworks/cn/web/wa-rails1/
 [4]: http://github.com/yrashk/caches.rb
 [5]: http://blog.zmok.net/articles/2008/01/29/utilizing-caches-rb-with-ferret