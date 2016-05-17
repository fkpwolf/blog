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

最近（2016.3.11）发现还可以直接用Github Page，不用发到自己的VPS上面。
如果发现/css/main.css找不到的情况，要配置baseurl，参考[这里](https://byparker.com/blog/2014/clearing-up-confusion-around-baseurl/)。Github Page需要只看gh-pages分支里面的东西，所以要先把master倒到gh-pages里面去。用[这个](http://www.stephaniehicks.com/githubPages_tutorial/pages/githubpages-jekyll.html)：

```
$ git branch -m master gh-pages 
$ git remote add origin git@github.com:username/myrepo.git
$ git push -u origin gh-pages
```

可能要先删除远程的gh-pages分支。每次修改master后需要pull merge到gh-pages分支上，这个有点多此一举。这样做是把pull request当做一个『正式发布』的命令？如果要同步这两个branch，可以参考[这里](http://stackoverflow.com/questions/5807459/github-mirroring-gh-pages-to-master)。

### 编辑
Github自己也有编辑器了，也支持MarkDown。感觉git已经被玩坏。我用app.classeur.io，可以直接更新到Github上面。不过界面更加小清新，也让人有写作的欲望。

这种新旧技术结合起来用，即便classeur.io不稳定，也没有关系，因为数据都是保存在Github上面。


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help


