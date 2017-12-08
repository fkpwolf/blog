---
layout: post
title:  "快速搭建Arduino小车"
date:   2015-10-26 16:27:00
---

## 缘起
儿子一直想搞个小车.

[<img src="http://fkpwolf.net/images/2015/arduino-moto.jpg" width="300px"/>][1]

## 各个部件
- ESP8266 WiFi 模块
- Arduino Mega，只有这个才有两个 UART 口，这样一个用来接 WiFi 模块，一个接马达控制板
- 马达控制板，常见的 L293D 控制芯片
- 手机控制 APP，类似“TCP连接”这种可以直接发送 TCP 请求到 ESP8266

## 过程
代码比较简单，主要是各个部件之间如何调试。因为部件比较多，这里要采用隔离测试、分布替换的方法。比如先用 PC 向 Arduino 发送命令来控制马达，成功后再换成 WiFi 控制。

