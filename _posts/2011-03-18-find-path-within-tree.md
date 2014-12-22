---
title: 树的路径查找算法
author: Fan Fan
layout: post
permalink: /2011/03/18/find-path-within-tree/
dsq_thread_id:
  - 691100579
categories:
  - CS
---
**问题**：对于树中的某个节点，找到他在树中的路径。比如如下图中Z的path为{0, 1, 3, 1}。

[<img class="alignnone size-full wp-image-928" title="fig457_01_0" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/03/fig457_01_0.jpg" alt="" width="300" height="92" />][1]

最原始的DFS算法为：

<pre class="brush:java">private static void getPath(List&lt;Entry&gt; list, ID id, ArrayList&lt;Integer&gt; matchPath,
int... path) {
  for (int i = 0; i &lt; list.size(); i++) {
    int length = path.length;
    int[] currentPath = new int[length + 1];
    System.arraycopy(path, 0, currentPath, 0, length);
    currentPath[length] = i;

    Entry entry = list.get(i);
    if (id.equals(entry.id)) {
      if (matchPath.size() == 0) { 
        for (int j : currentPath) {// clone
          matchPath.add(j);
        }
      }
    } else
       getPath(entry.getChildren(), id, matchPath, currentPath);
  }
}
</pre>

但是这种算法虽然完成了任务，但总觉得过于罗嗦。写好后三个月，又摸回去改了改，结果如下，使用了Stack来和DFS互动。

<pre class="brush:java">private static void getPath(List&lt;Entry&gt; list, ID id, Stack&lt;Integer&gt; path, Flag isFound) {
  for (int i = 0; i &lt; list.size() && !isFound.value; i++) {
    Entry entry = list.get(i);
    path.push(i);
    if (id.equals(entry.id)) {
      isFound.value = true;
      return;
    } else{
      getPath(entry.getChildren(), id, path, isFound);
    }
  }
  if(isFound.value)
    return;
  if ( !path.empty())
    path.pop();
}</pre>

简单的才是对的，要有这种信念。做人就得信，做&#8230;.

 [1]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/03/fig457_01_0.jpg