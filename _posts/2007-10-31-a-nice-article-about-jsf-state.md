---
title: A nice article about JSF state
author: Fan Fan
layout: post
permalink: /2007/10/31/a-nice-article-about-jsf-state/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/10/nice-article-about-jsf-state.html
categories:
  - Web Development
---
&nbsp; developerWorks recently posted an article: [Auto-save JSF forms with Ajax][1]. The article provided an approach to implement auto-save function(some like Word&#8217;s auto-save) in web tie. It told us where the data saved and how to recover from the saved data. This article used much JSF knowledge(some like [component state][2]) which is different from JSP tag-lib or other technology and in this case we can see JSF and Ajax is powerful.  
And more, in some other scenario where auto-save is no need. For example, the customer want to keep some crucial form and he can resume from it later, he can do it by click a &#8220;Save&#8221; button <span style="font-weight: bold">by himself</span>. Because auto-save isn&#8217;t needed everywhere. And auto-save sometimes makes customers confused. Only the customer thinks that he has finished the form <span style="font-weight: bold">nearly</span>, then recover from that point is constructive for him. Though AJAX is useless here, the way the author provided is also helpful and suggestive if JSF is still the choice of web framework.

&nbsp; So from the viewpoint of an end user, a good designed page should tell him, whether the current form is auto-save or not. Something should notify the user, whether the save action is success or failed. And the user can find all forms he has saved somewhere. So he can resume from there conveniently. At last the system looks maybe some like a workflow system.

 [1]: http://www.ibm.com/developerworks/cn/web/wa-aj-jsf1.html
 [2]: http://fkpwolf.blogspot.com/2007/05/question-on-jsf-state-saving.html