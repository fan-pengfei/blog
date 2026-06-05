---
title: "C语言实现交换二进制数的任意两位"
date: 2023-04-17T10:34:30.000Z
draft: false
tags: ["C语言"]
---
> 最近在用LED组成的数码管，由于位号硬件上有所改动，因而需要进行码值位之间的交换；

以下是一段C语言函数，实现将一个八位的二进制数的任意两位交换：

```c
#include
/**
 * @brief   交换一个八位的二进制数的任意两位
 * @param x 一个八位的二进制数
 * @param i 要交换的两个位置
 * @param j 要交换的两个位置
 * @return  交换后的结果
 */
unsigned char swap_bits(unsigned char x, int i, int j)
{
    // 获取第i位和第j位的值
    unsigned char bit_i = (x >> i) & 1;
    unsigned char bit_j = (x >> j) & 1;
    // 如果第i位和第j位的值不同，那么交换它们
    if (bit_i ^ bit_j)
    {
        x ^= (1
