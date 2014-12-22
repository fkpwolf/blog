---
title: Question on JSF state saving
author: Fan Fan
layout: post
permalink: /2007/05/07/question-on-jsf-state-saving/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/05/question-on-jsf-state-saving.html
dsq_thread_id:
  - 608974751
categories:
  - Web Development
---
JSF state is restored or used in &#8220;Restore View&#8221; of JSF life cycle. These state can be stored in 2 places:client and server. There are some links about the difference of them:

[http://www.jroller.com/page/mert?entry=state\_saving\_method\_client\_side][1]  
[http://www.jroller.com/page/cagataycivici?entry=jsf\_state\_saving\_best\_of][2]  
[http://www.jroller.com/page/cenkcivici?entry=changing\_default\_state\_management\_method][3] 

These states are used to store data about the JSF component tree(the elements in f:view), including components and component id. They can be seen between 2 request. That is the difference from backend bean. At first thought most of components have no state, or just have a value which presents its state. A text field has state? But YES. Disable/Enable is a common state. In the component implement java code, <span style="font-style: italic;">saveState</span> and <span style="font-style: italic;">restoreState</span> methods deal all these work: choose some attributes and save them to an object which can be found as a hidden field named <span style="font-style: italic;">javax.faces.ViewState</span> in client&#8217;s browser source code. It was Base64 encoding. And of course, the view state has been configurated to be stored in the client. In Sun&#8217;s webui implement, many attributes are stored in view state, such as javascript event method and style.

Some component such as Table can be very complex and has its own states such as the current page number and ordered column. If these states doesn&#8217;t be stored in component tree, we have to save them in hidden input field.

Myfaces even has a component t:saveState<t:savestate> just for storing data. See [here][4].  
</t:savestate>

This technology is powerful when user&#8217;s action is within one page. When page is refreshed, all he has done are saved. If he just jumps between page, navigate from one page to another page, then the state of a page is discarded, and more it is useless. In one page scenario, AJAX is also a good choice. They all improve user&#8217;s experience.

Somebody provides [stateless approach][5]. 

There is a cool tool called [FacesTrace][6] to monitor JSF application. It displays not only session data, but also the whole JSF life cycle dynamically.

 [1]: http://www.jroller.com/page/mert?entry=state_saving_method_client_side
 [2]: http://www.jroller.com/page/cagataycivici?entry=jsf_state_saving_best_of
 [3]: http://www.jroller.com/page/cenkcivici?entry=changing_default_state_management_method
 [4]: http://wiki.apache.org/myfaces/How_JSF_State_Management_Works
 [5]: http://weblogs.java.net/blog/jhook/archive/2006/01/experiment_goin_1.html
 [6]: http://facestrace.sourceforge.net/