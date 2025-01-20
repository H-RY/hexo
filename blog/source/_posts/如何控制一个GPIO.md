---
title: 如何控制一个GPIO
date: 2025-01-07 15:27:07
tags:
---

## 如何控制一个GPIO

路径：/sys/class/gpio 下调节GPIO

pin脚值 = （B - A）* 32 + 2 （ASCII码）



算出后用 echo 34 > export

然后进入GPIO文件夹



active_low：0高电平有效，1低电平有效

direction：in输入，out输出（cpu端）

value：高电平1，低电平0
