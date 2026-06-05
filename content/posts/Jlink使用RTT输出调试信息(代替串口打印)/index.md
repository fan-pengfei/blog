---
title: "Jlink使用RTT输出调试信息(代替串口打印)"
date: 2022-02-15T04:02:47.000Z
draft: false
tags: ["DIY", "stm32"]
---
> Jlink RTT调试技巧；

## 使用Jlink的 RTT功能 :

这个功能是不需要另外接其他引脚的，如果使用SW连接方式，仅仅两根线就可以。

RTT 是Jlink的一种实时终端的方式连接输出调试信息，网上有很多说明之间按照做就可以，我仅仅是记录一下自己的步骤.

> 就是下载RTT软件包，下载RTT文件： [http://download.segger.com/J-Link/RTT/RTT_Implementation_140925.zip](http://download.segger.com/J-Link/RTT/RTT_Implementation_140925.zip)  ；

![img](img-1.png)

> 添加RTT文件到自己的工程：
> 添加必要的头文件：

![](img-2.png)

> 输出函数打印：

![](img-3.png)

这个时候RTT在程序中就添加成功了，我们可以使用使用Jlink带的工具进行查看数据；

如打开RTT Viewer 提升连接，点击OK 不出意外的话，你就可以看到调试信息了；

![](img-4.png)

![](img-5.png)
