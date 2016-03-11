ucf 定义了外部的资源，比如
```
#CLOCK
NET "CLK50M" LOC = "P55";

#IO_B
NET "KEY[1]"   LOC = "P124";#        KEY1
NET "KEY[0]"   LOC = "P126";#LCD_RS, KEY0
NET "LED[0]"   LOC = "P127";#LCD_RW, LED0
NET "LED[1]"   LOC = "P131";#LCD_E , LED1

NET "CLK50M" TNM_NET = "TNM_CLK";
TIMESPEC "TS_CLK" = PERIOD "TNM_CLK" 20 ns HIGH 50%;
```
所有的assign 和always 块都是并行发生的。每个都是一个独立的门？
