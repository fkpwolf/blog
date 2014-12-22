---
title: 在dojo中创建一个button的三种方式
author: Fan Fan
layout: post
permalink: /2008/04/20/%e5%9c%a8dojo%e4%b8%ad%e5%88%9b%e5%bb%ba%e4%b8%80%e4%b8%aabutton%e7%9a%84%e4%b8%89%e7%a7%8d%e6%96%b9%e5%bc%8f/
dsq_thread_id:
  - 615908269
categories:
  - Web Development
---
初学dojo，做个笔记。因为dojo的相关文档很少，官方的写的也很凌乱。 全部的代码如下：

<pre><!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
<head>
	<meta http-equiv="Content-type" content="text/html; charset=utf-8" />
	<title>Sample 1.0.0 xdomain page</title>
	<script type="text/javascript"
		djConfig="isDebug: true, parseOnLoad: true"
		src="http://o.aolcdn.com/dojo/1.0.0/dojo/dojo.xd.js"></script>
	<script type="text/javascript">
		dojo.require("dijit.form.Button");
		dojo.require("dijit.InlineEditBox");
		dojo.require("dijit.form.TextBox");

		Commands = {
			sayHello: function(){
				var status = "Button Clicked at time: " + (new Date());
				dojo.byId("output").innerHTML = status;
				console.debug(status);
			}
		}

		dojo.addOnLoad(function(){
			dojo.connect(dojo.byId("testButton"), "onclick", Commands, "sayHello");
       			new dijit.form.Button({ "class": "large", label:"wokao",onClick:foo }, dojo.byId("id1"));
			new dijit.InlineEditBox({onChange:myHandler, autoSave:false},
				dojo.byId("id2"));
		});
	</script>
	<script type="text/javascript">
		function foo(){
			console.debug("click foo");
		}
		function myHandler(idOfBox, value) {
          		console.debug("Edited value from "+idOfBox+" is now "+value);
       		}

	</script>
    <style type="text/css">
        @import "http://o.aolcdn.com/dojo/1.0.0/dijit/themes/tundra/tundra.css";
        @import "http://o.aolcdn.com/dojo/1.0.0/dojo/resources/dojo.css";
    </style>

</head>
<body>
	<h1>Sample Dojo 1.0.0 xdomain page</h1>
	<p>
		<button id="testButton">Click Me</button>
	</p>
	<p id="output">
	</p>
	<button dojoType="dijit.form.Button" id="helloButton">Hello World!</button>
	<span id="id1"></span>
	<h3 id="editable" autosave="false"
                dojoType="dijit.InlineEditBox" title="h3 example"
                onChange="myHandler(this.id,arguments[0])">
		Edit me - I trigger the onChange callback
	</h3>
	<span id="id2">programatic InlineEditBox</span>
</body>
</html>

第一种是HTML的产生方式。
第二种是使用dojoType的方式。可将这种方式是基于标准HTML的一种扩展，也是一种静态的方式。


<pre><button dojoType="dijit.form.Button" id="helloButton">Hello World!</button>
跟Tapestry有点类似，其优点是一般的图形化的html编辑器也可以对其进行编辑。对于这种方式，需要在dojo的配置里面设置“parseOnLoad: true”。
第三种是使用编程的方式（programmatically），把属性的值统一的传给构造函数。


<pre>new dijit.form.Button({ "class": "large", label:"wokao",onClick:foo }, dojo.byId("id1"));</pre>