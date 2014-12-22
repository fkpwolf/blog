---
title: use ajax4jsf
author: Fan Fan
layout: post
permalink: /2007/05/03/use-ajax4jsf/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/05/use-ajax4jsf.html
categories:
  - AJAX
---
<a href="http://labs.jboss.com/jbossajax4jsf/" target="_blank">Ajax4JSF</a> is a framework which inserts AJAX function to JSF. It is easy to transform orginal JSF code to Ajax-able. Ajax4JSF just changed the way of http message.  
It&#8217;s document is <a href="http://labs.jboss.com/file-access/default/members/jbossajax4jsf/freezone/docs/devguide/index.html" target="_blank">here</a>. It has a simple example. Look at blow code: <?xml:namespace prefix = a4j /><a4j:commandbutton rerender="rep2" value="Link" action="#{Page1.do2}">

<?xml:namespace prefix = h /><h:outputtext id="rep2" value="#{RequestBean1.name}" binding="#{Page1.rep2}">

  
the reRender property points out whose data in JSF component tree should be return from server. After JSF engine renders that component, Ajax4JSF then sends response. So this approach didn&#8217;t do harsh to JSF. We can code in JSF bean:   
`<br />private HtmlOutputText rep2 = new HtmlOutputText(); <br />...... <br />String style="border-color: rgb(204, 0, 255); backgroun....."; <br />this.rep2.setStyle(style); <br />`  
We can see the benefit of JSF component model. But this model don&#8217;t consider the position of the javascript or it is hard to add javascript function. For example, if we want to hightlight the outputText, Should I write a new class extends HtmlOutputText which has a cool appearance? Maybe a Decrtor Pattern is better. No matter which approach we use, we should encapsulate the javascript into JSF component and give user a consistent usage. On the other hand, If we have a JSF component which is not ajax-able but provides hightlight function, we can use it directly in Ajax4JSF. So the problem is the matter of JSF instead of Ajax4JSF.

</h:outputtext></a4j:commandbutton><a4j:commandbutton rerender="rep2" value="Link" action="#{Page1.do2}"><h:outputtext id="rep2" value="#{RequestBean1.name}" binding="#{Page1.rep2}"></h:outputtext></a4j:commandbutton>