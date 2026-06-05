---
title: "union和struct存放格式"
date: 2022-05-06T08:55:37.000Z
draft: false
tags: ["C语言"]
---
> union、struct所占的空间大小；

## union:

​        **当多个数据需要共享内存或者多个数据每次只取其一时，可以利用联合体(union)。在C Programming Language 一书中对于联合体是这么描述的：**

> **联合体是一个结构；**
> **它的所有成员相对于基地址的偏移量都为0；**
> **此结构空间要大到足够容纳最”宽”的成员；**
> **其对齐方式要适合其中所有的成员；**

​       下面解释这四条描述：

> 由于联合体中的所有成员是共享一段内存的，因此每个成员的存放首地址相对于于联合体变量的基地址的偏移量为0，即所有成员的首地址都是一样的。为了使得所有成员能够共享一段内存，因此该空间必须足够容纳这些成员中最宽的成员。对于这句“对齐方式要适合其中所有的成员”是指其必须符合所有成员的自身对齐方式。

例如：

```c++
#include
using namespace std;
union A{
   int a[5];
   char b;
   double c;
};
int main(){
   cout

**a**

**b**
**b**
**b**
**b**

**c**
**c**

```c++
//例如：
struct B
{
    char a;
    short b;
    int c;
}
//则sizeof(A)大小应该是4+4=8字节
```

**a**
**c**
**c**

**b**
**b**
**b**
**b**

```c++
//例如：
struct B
{
    char a;
    char b[2];
    char c[4];
}
//则sizeof(A)大小应该是1+2+4=7字节
```

a
b
b
c
c
c
c

**结构体在内存中的存放也是按照单元存放的，所以其开辟的单元的最大长度取决于占字节数最大的数据类型，而且存储顺序对于空间利用率有一些影响。**

## 总结：

**混合结构体的计算：**

```c++
#include
typedef union{
	long i;
    int k[5];
    char c;
}UDATE;
struct data{
    int cat;
    UDATE cow;
    double dog;
}too;
UDATE temp;
int main()
{
    printf("%d,%d",sizeof(temp),sizeof(too));
}
```

**结果是：24，40；**

**前一个结果，联合体，最大单元为8，8对齐，4*5=20，最接近20的8的倍数为24；**

**后一个结果，8+24+8=40；**

**cat占用4个，cow占用24个，cow对齐方式为8字节，所以cat后边要填充4个字节，即cat和cow共占用8+24=32个，再加上dog占的8个字节，所以一共是40个字节。**

## __packed

​        像union和struct这样的数据结构，总有一些字节是浪费掉的，这样做的目的很简单，就是因为在大多数计算机体系结构中，对内存操作时按整字存取才能达到最高效率，相当于是以空间换取时间。

​        当然在某些计算机体系结构中，比如ARM，是支持非对齐字传输的，也就是说变量并不一定要按照字长对齐，尽管这样可能会降低效率，但换来的是存储空间上的节约。在mdk中加上__packed关键字，可以得到非对齐字的紧凑型结构体，则会强制编译器将结构体成员按1字节对齐，则以下结构体占用空间仍为15字节。

```c
typedef __packed struct {
    char a;   	 //1byte
    int b;   	 //4byte
    char c[2]    //2byte
    double d;    //8byte
}Test;
```

> 如果编译器不支持__packed关键字，将其定义为空宏即可  #define  __packed.

```c
#pragma pack (1) /*指定按1字节对齐*/
#pragma pack () /*取消指定对齐，恢复缺省对齐*/
```

## 位域：

C语言标准规定，位域的宽度不能超过它所依附的数据类型的长度。通俗地讲，成员变量都是有类型的，这个类型限制了成员变量的最大长度，`:`后面的数字不能超过这个长度。

C语言标准还规定，只有有限的几种数据类型可以用于位域。在 ANSI C 中，这几种数据类型是 int、signed int 和 unsigned int（int 默认就是 signed int）；到了 C99，_Bool 也被支持了。

但编译器在具体实现时都进行了扩展，额外支持了 char、signed char、unsigned char 以及 enum 类型，所以上面的代码虽然不符合C语言标准，但它依然能够被编译器支持。

### 位域的内存分配：

位域的具体存储规则如下：

**1、当相邻成员的类型相同时，如果它们的位宽之和小于类型的 sizeof 大小，那么后面的成员紧邻前一个成员存储，直到不能容纳为止；如果它们的位宽之和大于类型的 sizeof 大小，那么后面的成员将从新的存储单元开始，其偏移量为类型大小的整数倍。**

```c
#include
int main(){
    struct bs{
        unsigned m: 6;
        unsigned n: 12;
        unsigned p: 4;
    };
    printf("%d\n", sizeof(struct bs));
    return 0;
}
```

> 运行结果：4

m、n、p 的类型都是 unsigned int，sizeof 的结果为 4 个字节（Byte），也即 32 个位（Bit）。m、n、p 的位宽之和为 6+12+4 = 22，小于 32，所以它们会挨着存储，中间没有缝隙。

> sizeof(struct bs) 的大小之所以为 4，而不是 3，是因为要将内存对齐到 4 个字节，以便提高存取效率；

如果将成员 m 的位宽改为 22，那么输出结果将会是 8，因为 22+12 = 34，大于 32，n 会从新的位置开始存储，相对 m 的偏移量是 sizeof(unsigned int)，也即 4 个字节。

如果再将成员 p 的位宽也改为 22，那么输出结果将会是 12，三个成员都不会挨着存储。

**2、当相邻成员的类型不同时，不同的编译器有不同的实现方案，GCC会压缩存储，而 VC/VS 不会。**

```c
#include
int main(){
    struct bs{
        unsigned m: 12;
        unsigned char ch: 4;
        unsigned p: 4;
    };
    printf("%d\n", sizeof(struct bs));
    return 0;
}
```

> 在 GCC 下的运行结果为 4，三个成员挨着存储；在 VC/VS 下的运行结果为 12，三个成员按照各自的类型存储（与不指定位宽时的存储方式相同）。

**3、如果成员之间穿插着非位域成员，那么不会进行压缩。例如以下：**

```c
struct bs{
    unsigned m: 12;
    unsigned ch;
    unsigned p: 4;
};
```

> 在各个编译器下 sizeof 的结果都是 12。

**4、位域成员可以没有名称，只给出数据类型和位宽，如下所示：**

```c
struct bs{
    int m: 12;
    int  : 20;  //该位域成员不能使用
    int n: 4;
};
```

> 无名位域一般用来作填充或者调整成员位置。
