---
layout: post
title:  "Welcome to Jekyll!"
date:   2014-12-22 14:20:33
categories: jekyll update
---
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

----------

从WordPress迁移过来（使用插件），感觉还是不错的。否则想到备份原来的MySQL我就打退堂鼓了。
轻量级的东西一直是我的最爱。

1. 目录的问题。如果所有的文章都放在_posts下面，图片也放在那里么？这样可能会造成混乱。
后来发现，文章可以放到_posts/2014/2014-11-11-test.markdown，而图片是按照绝对路径来的。
2. 翻页。试了下官方的方法，还没有成功。不过首页只是文章title的列表，加载很快。
3. 同步Evernote。用MarkDown还要用“马克飞象”的插件，似乎很繁琐。后来我觉得Evernote还是适合“及时”的编辑，MarkDown这种要反复修改的就没有必要放到Evernote里面了。

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
