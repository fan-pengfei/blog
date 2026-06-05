---
title: "FOC基础源码分析"
date: 2022-11-21T08:05:05.000Z
draft: false
tags: ["FOC"]
---
> 以基于STM32F103的FOC源码为例分析；

### 保存电机参数

采用宏定义开关来决定是否要进行参数校准；

```c
//1 已校准
//0 未校准
#define IS_ALIGN 0

//参数数据结构体
struct ALIGN_DATA
{
    long direction;
    int pole;
    float zero_angle;
} DATA_ALIGN;

//根据宏定义判断是否要更新FLASH中存储的参数
#if IS_ALIGN
    Internal_ReadFlash(ALIGN_ANGLE_ADDR, (uint32_t *)&DATA_ALIGN, 3);//读取内部FLASH中的参数
    sensor_direction = DATA_ALIGN.direction;    //方向
    pole_pairs = DATA_ALIGN.pole;               //极对数
    zero_electric_angle = DATA_ALIGN.zero_angle;//零点角度
#else
    //
    //此处略去参数识别代码
    //
    DATA_ALIGN.direction = sensor_direction;    //方向
    DATA_ALIGN.pole = pole_pairs;               //极对数
    DATA_ALIGN.zero_angle = zero_electric_angle;//零点角度
    Internal_WriteFlash(ALIGN_ANGLE_ADDR, (uint32_t *)&DATA_ALIGN, 3);//将参数写入单片机内部FLASH中
```

### 通过UART通信控制电机

```c
/**
 * @brief 简易串口命令接收，需在while循环里重复调用该函数
 * @return 无
 */
void commander_run(void)
{
    if ((USART_RX_STA & 0x8000) != 0)
    {
        switch (USART_RX_BUF[0])
        {
        case 'H':
            printf("Hello World!\r\n");
            break;
        case 'T': // T6.28
            target = atof((const char *)(USART_RX_BUF + 1));
            printf("RX=%.4f\r\n", target);
            break;
        case 'D': // D
            M1_Disable;
            printf("OK!\r\n");
            break;
        case 'E': // E
            M1_Enable;
            printf("OK!\r\n");
            break;
        }
        USART_RX_STA = 0;
    }
}

#define USART_REC_LEN 256
unsigned char USART_RX_BUF[USART_REC_LEN]; //接收缓冲，usart.h中定义长度
//接收状态
// bit15  接收完成标志
// bit14  接收到0x0D
// bit13~0  接收的字节数
unsigned short USART_RX_STA = 0; //接收状态标志
//发送的命令必须以"\r\n"作为结尾，以标志命令的结束
void USART1_IRQHandler(void) //串口1中断程序
{
    unsigned char Res;
    if (USART_GetITStatus(USART1, USART_IT_RXNE) != RESET) //
    {
        Res = USART_ReceiveData(USART1); //读取接收到的字节
        if ((USART_RX_STA & 0x8000) == 0) //接收未完成
        {
            if (USART_RX_STA & 0x4000) //接收到了0x0d
            {
                if (Res != 0x0a)
                {
                    USART_RX_STA = 0; //接收错误，重新开始
                }
                else
                {
                    USART_RX_STA |= 0x8000;                     //接收完成
                    USART_RX_BUF[USART_RX_STA & 0X3FFF] = '\0'; //最后一个字节放'0’，方便判断
                }
            }
            else //还没收到0x0D
            {
                if (Res == 0x0d)
                {
                    USART_RX_STA |= 0x4000;
                }
                else
                {
                    USART_RX_BUF[USART_RX_STA & 0X3FFF] = Res;
                    USART_RX_STA++;
                    if (USART_RX_STA > (USART_REC_LEN - 1))
                    {
                        USART_RX_STA = 0; //接收错误，重新开始
                    }
                }
            }
        }
    }
}
```
