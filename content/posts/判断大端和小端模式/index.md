---
title: "判断大端和小端模式"
date: 2022-05-09T13:28:46.000Z
draft: false
tags: ["计算机组成"]
---
> 测试自己的电脑是小端模式还是大端模式；

## 方法一：地址转换

将int 48存起来，然后取得其地址，再将这个地址转为char* ，这时候，如果是小端存储，那么char*指针就指向48；

> 48对应的ASCII码为字符`0`；

```c
void judge_bigend_littleend1()
{
    int i = 48;
    int* p = &i;
    char c = 0;
    c = *((char*)p);
    if (c == '0')
        printf("小端\n");
    else
        printf("大端\n");
}
```

## 方法二：同方法一

定义变量int i=1;将 i 的地址拿到，强转成char*型，这时候就取到了 i 的低地址，这时候如果是1就是小端存储，如果是0就是大端存储。

```c
void judge_bigend_littleend2()
{
    int i = 1;
    char c = (*(char*)&i);
    if (c)
        printf("小端\n");
    else
        printf("大端\n");
}
```

## 方法三：利用联合体对齐规则

定义联合体，一个成员是多字节，一个是单字节，给多字节的成员赋一个最低一个字节不为0，其他字节为0 的值，再用第二个成员来判断，如果第二个字节不为0，就是小端，若为0，就是大端。

```c
void judge_bigend_littleend3()//因为联合体小的总是在低位
{
    union
    {
        int i;
        char c;
    }un;
    un.i = 1;
    if (un.c == 1)
        printf("小端\n");
    else
        printf("大端\n");
}
```
