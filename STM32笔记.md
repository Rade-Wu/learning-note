# STM32

## 1.寄存器

![image-20210506161939642](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210506161939642.png)

偏移地址是指本寄存器相对于这个外设的基地址的偏移

![image-20210505111939748](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210505111939748.png)

![image-20210505112220887](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210505112220887.png)

![image-20210505112242661](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210505112242661.png)

![image-20210505112314664](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210505112314664.png)

## 1.1 寄存器点灯

1.1.1

代码中使用了“&=~”、“|=”这种操作方法是为了避免影响到寄存器中的其它位。

因为寄存器不能按位读写，假如我们直接给 CRL 寄存器赋值： GPIOC_CRL = 0x0000 0100;

这时 CRL 的的低 8~11 位被设置成“0001”输出模式，但其它 GPIO 引脚就有意见了，因

为其它引脚的 MODER 位都已被设置成输入模式(即全部被清 0)。所以，为了在配置寄存器

某个位的时候不影响其它位，推荐使用“&=~”和“|=”这两种操作方式，其中“&=~”用

于清 0，“|=”用于置 1。

## 2.固件库

![image-20210508171847467](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210508171847467.png)

![image-20210511232401889](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210511232401889.png)

![image-20210513163801434](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210513163801434.png)

### 1.GPIO输入检测（按键）

#### 1.1 输入模式

浮空输入的电平是不确定的，完全由外

部的输入决定，一般接按键的时候用的是这个模式。

### 2.位带操作

![image-20210522100057452](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210522100057452.png)

### 3.RCC-时钟

![image-20210522150620199](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210522150620199.png)

#### 1.HSE配置时钟

步骤：

1.复位

2.使能HSE

3.等待HSE就绪（失败另设）

4.Flash使能预处置，配置等待

5.配置AHB、APB1、APB2分屏因子

6.锁相环（PLL）配置（时钟源，倍频）

7.使能PLL

8.等待PLL稳定

9.选择PLLCLK为系统时钟

10.等待PLLCLK切换为系统时钟

### 4.中断

![image-20210523181558931](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210523181558931.png)

#### 4.1 EXTI 外部中断/事件控制器

![image-20210524215111197](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210524215111197.png)

 ![image-20210524215539069](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210524215539069.png)

##### 1.外部中断控制实验

![image-20210525172053216](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210525172053216.png)

###### 1.1NVIC中断编程

在配置每个中断的时候一般有 3 个编程要点：

1、使能外设某个中断，这个具体由每个外设的相关中断使能位控制。比如串口有发送

完成中断，接收完成中断，这两个中断都由串口控制寄存器的相关中断使能位控制。

2、初始化 NVIC_InitTypeDef 结构体，配置中断优先级分组，设置抢占优先级和子优

先级，使能中断请求。NVIC_InitTypeDef 结构体在固件库头文件 misc.h 中定义。

3、编写中断服务函数

在启动文件 startup_stm32f10x_hd.s 中我们预先为每个中断都写了一个中断服务函数，

只是这些中断函数都是为空，为的只是初始化中断向量表。实际的中断服务函数都需要我

们重新编写，为了方便管理我们把中断服务函数统一写在 stm32f10x_it.c 这个库文件中。

关于中断服务函数的函数名必须跟启动文件里面预先设置的一样，如果写错，系统就

在中断向量表中找不到中断服务函数的入口，直接跳转到启动文件里面预先写好的空函数，

并且在里面无限循环，实现不了中断。

### 5.Sys Tick--系统定时器

#### 5.1 框图

![image-20210526195305946](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210526195305946.png)

#### 5.2 SysTick 寄存器

![image-20210526195917255](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210526195917255.png)

![image-20210526195941506](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210526195941506.png)

#### 5.3 SysTick 定时时间计算

![image-20210526200056870](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210526200056870.png)

#### 5.4 软件设计

![image-20210526200727014](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210526200727014.png)

![image-20210526230246623](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210526230246623.png)

## 6.通讯

#### 6.1通讯基本概念

1.根据传播方式

![image-20210527102403209](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210527102403209.png)

2.根据通讯方向

![image-20210527102759172](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210527102759172.png)

3.根据通讯的数据同步方式

3.1 同步

![image-20210527103159344](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210527103159344.png)

3.2 异步

![image-20210527103304988](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210527103304988.png)

4.通讯速率

![image-20210527103530014](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210527103530014.png)

#### 6.2 USART串口通讯

1.基本介绍

通用同步异步收发器(Universal Synchronous Asynchronous Receiver and Transmitter)是一

个串行通信设备，可以灵活地与外部设备进行全双工数据交换。有别于 USART 还有一个

UART(Universal Asynchronous Receiver and Transmitter)，它是在 USART 基础上裁剪掉了同

步通信功能，只有异步通信。简单区分同步和异步就是看通信时需不需要对外提供时钟输

出，我们平时用的串口通信基本都是 UART。

2.功能框图

![image-20210529103238610](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210529103238610.png)

![image-20210613105222732](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210613105222732.png)

3.实验

1.

首先，我们来编写一个程序实现开发板与电脑通信，在开发板上电时通过 USART 发

送一串字符串给电脑，然后开发板进入中断接收等待状态，如果电脑有发送数据过来，开

发板就会产生中断，我们在中断服务函数接收数据，并马上把数据返回发送给电脑。

![image-20210529114746197](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210529114746197.png)

2.usart中断和事件

![image-20210529205008391](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210529205008391.png)

#### 6.3 DMA

![image-20210613105333119](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210613105333119.png)

1.M2M

![image-20210610105653818](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210610105653818.png)

2.M2P

![image-20210613121225668](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210613121225668.png)

#### 6.4 I2C

![image-20210615164507656](C:\Users\86158\AppData\Roaming\Typora\typora-user-images\image-20210615164507656.png)
