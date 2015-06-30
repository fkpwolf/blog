---
title: 实现DSL的几个例子
author: Fan Fan
layout: post
permalink: /2009/12/31/example-of-dsl/
dsq_thread_id:
  - 629711154
categories:
  - Language
---
所谓DSL就是能描述语言的语言，一般某些领域都有自己的专有术语，用这些术语来交流比一般的通用的语言更流畅，比如象棋的“平五进八”，这种走棋的表达比自然语言更快，DSL就是把这种领域的语言翻译成“自然语言”的模型。一般有内部和外部之分，内部指领域语言嵌入在自然语言中用自然语言表达，相当于从“古文”中进化出“普通话”的过程；外部则指全新的语言，比如Java, C等，是一个全新的描述模型，创造起来比内部要复杂。举几个例子容易明白，下面几个是内部的DSL。

  * 使用连贯接口
这是&#8221;The productive Programmer&#8221;中的例子，要描述的领域是“食谱”。比如“recipe.add 200.grams.of Flour”。

<pre class="brush:ruby">class Numeric
  def gram
   self
  end

  alias_method :grams, :gram
end</pre>

Ruby可以打开一个存在的类向其中添加方法，所以可以写出200.grams这种有点恶心的句子，而且ruby中方法可以不加括号（），光这点用Java实现就够呛。不过用method chain会使用很多点点点，有点像人没有进化完全而留下点阑尾&#8230;

这种open class的做法在其他语言中也可以遇到。Javascript中是直接修改类的prototype，Objective-C中也可以，比如扩展NSString使其可以转化为Json格式。但是[Open class也有问题][1]，因为Open class直接修改了原来的类，这会蔓延到整个类型系统，违反了模块化和封装的原则，所以这个修改最后只在某个范围内有效。

  * 下面这个例子来自&#8221;Programming Scala Tackle Multi-Core Complexity on the Java Virtual Machine&#8221;，要描述的领域为“**时间**”，比如“2 days ago”。
<pre class="brush:ruby">object App
{
  def main(args : Array[String]) : Unit =
  {
    implicit def convertInt2DateHelper(number: Int) = new DateHelper(number)
	val ago = "ago"
	val from_now = "from_now"
	val past = 2 days ago
	val appointment = 5 days from_now
	println(past)
	println(appointment)
  }

	import java.util._
	class DateHelper(number: Int) {
		def days(when: String) : Date = {
			var date = Calendar.getInstance()
			when match {
				case "ago" =&gt; date.add(Calendar.DAY_OF_MONTH, -number)
				case "from_now" =&gt; date.add(Calendar.DAY_OF_MONTH, number)
				case _ =&gt; date
			}
			date.getTime()
		}
  	}
}</pre>

这里面当Scala遇到Int类型的2时，会自动的选择implicit的方法把这个整数转换为DateHelper类，不清楚这是语言的功能还是VM的特征，这里并没有扩展Int类型：加上days的方法。Scala有个Traits的功能，倒是可以扩展类，暂时不大清楚。

  * 计算器
这里有讲到：[面向 Java 开发人员的 Scala 指南: 构建计算器，第 1 部分][2] 解决如何计算如((5 * 10) + 7)这样的表达式。这倒是一个DSL的好例子，不过文章最后把核心问题交给了Becker-Naur Form（BNF），这就有点走向外部DSL的，这么简单的表达式也很难搞，看来内部DSL不能描述复杂的语法结构，高级一点的又得求助于语法解析器。

 [1]: http://www.infoq.com/articles/ruby-open-classes-monkeypatching
 [2]: http://www.ibm.com/developerworks/cn/java/j-scala08268.html