<p>“FPGA设计中的约束文件有3类：用户设计文件（.UCF文件）、网表约束文件（.NCF文件）以及物理约束文件（.PCF文件），可以完成时序约束、管脚约束以及区域约束。3类约束文件的关系为：用户在设计输入阶段编写UCF文件，然后UCF文件和设计综合后生成NCF文件，最后再经过实现后生成PCF 文件。”<br>
.ucf 定义了外部的资源，比如</p>
<pre><code>#CLOCK
NET "CLK50M" LOC = "P55";

#IO_B
NET "KEY[1]"   LOC = "P124";#        KEY1
NET "KEY[0]"   LOC = "P126";#LCD_RS, KEY0
NET "LED[0]"   LOC = "P127";#LCD_RW, LED0
NET "LED[1]"   LOC = "P131";#LCD_E , LED1

NET "CLK50M" TNM_NET = "TNM_CLK";
TIMESPEC "TS_CLK" = PERIOD "TNM_CLK" 20 ns HIGH 50%;
</code></pre>
<p>所有的assign 和always 块都是并行发生的。每个都是一个独立的门？</p>
<blockquote>
<p>FPGA是由查找表和触发器来构成</p>
</blockquote>
<blockquote>
<p>“我认为VHDL比较严谨，Verilog比较灵活。VHDL与Verilog的区别有点与汇编语言和C语言类似。芯片设计厂商通常都用Verilog，很少有用VHDL的。”</p>
</blockquote>
<blockquote>
<p>3、设计一个小代码，下载到目标板看看结果<br>
此时可以设计一个最简答的程序，譬如点灯。如果灯在闪烁了，表示基本入门了。如果此时能够下载到fpga外挂的flash，fpga程序能够从flash启动,表明fpga的最简单设计你已经成功，可以到下一步。<br>
4、设计稍微复杂的代码，下载到目标板看看结果。<br>
可以设计一个UART程序，网上有参考，你要懂RS232协议和fpga内置的逻辑分析仪。网上下载一个串口调试助手，调试一番，如果通信成功了，  恭喜，水平有提高。进入下一步。<br>
5、设计复杂的代码，下载到目标板看看结果。<br>
譬如sdram的程序，网上也有参考，这个设计难度有点大。可用串口来调试sdram，把串口的数据存储到sdram，然后读回，如果成功，那你就比较熟悉fpga的设计饿了</p>
</blockquote>
<p>hehe</p>
