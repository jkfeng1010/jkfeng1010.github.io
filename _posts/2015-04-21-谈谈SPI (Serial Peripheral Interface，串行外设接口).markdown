---
title:  "谈谈SPI (Serial Peripheral Interface，串行外设接口)"
subtitle: "A Beautiful shot during the night."
author: "Wferr"
avatar: "img/authors/wferr.png"
image: "img/SPI.png"
date:   2015-04-21 12:12:12
---

### 谈谈SPI (Serial Peripheral Interface，串行外设接口)
今天我们来一起聊聊谈谈SPI (Serial Peripheral Interface，串行外设接口)；


----------

**什么是SPI**
----------

SPI (Serial Peripheral Interface，串行外设接口)是Motorola 公司推出的一 
种同步串行接口技术，是一种高速的，全双工，同步的通信总线；

它以主从方式工作，这种模式通常有一个主设备和一个或多个从设备，需要至少4根线，事实上3根也可以（单向传输时）。

（1）SDI – SerialData In,串行数据输入；
（2）SDO – SerialDataOut,串行数据输出；
（3）SCLK – Serial Clock,时钟信号，由主设备产生；
（4）CS – Chip Select,从设备使能信号，由主设备控制。


两种模式的图示![这里写图片描述](https://img-blog.csdn.net/20180507200356355?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在点对点的通信中，SPI接口不需要进行寻址操作，且为全双工通信，显得简单高效。
在多个从器件的系统中，每个从器件需要独立的使能信号，硬件上比I2C系统要稍微复杂一些。

![这里写图片描述](https://img-blog.csdn.net/20180507194917845?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


----------

**使用指南和简单应用**
--------

使用到了SPI的4根信号线和电源便可
![这里写图片描述](https://img-blog.csdn.net/20180507195226710?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

SPI有四种操作模式——模式0、模式1、模式2和模式3，它们的区别是定义了在时钟脉冲的哪条边沿转换（toggles）输出信号，哪条边沿采样输入信号，还有时钟脉冲的稳定电平值（就是时钟信号无效时是高还是低）。每种模式由一对参数刻画，它们称为时钟极（clock polarity）CPOL与时钟期（clock phase）CPHA。


Mode0：CPOL=0，CPHA=0
Mode1：CPOL=0，CPHA=1
Mode2：CPOL=1，CPHA=0
Mode3：CPOL=1，CPHA=1

![这里写图片描述](https://img-blog.csdn.net/2018050720070552?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

> 转载一段认为比较好的解释

时钟极性CPOL是用来配置SCLK的电平出于哪种状态时是空闲态或者有效态，时钟相位CPHA是用来配置数据采样是在第几个边沿：

CPOL=0，表示当SCLK=0时处于空闲态，所以有效状态就是SCLK处于高电平时
CPOL=1，表示当SCLK=1时处于空闲态，所以有效状态就是SCLK处于低电平时
CPHA=0，表示数据采样是在第1个边沿，数据发送在第2个边沿
CPHA=1，表示数据采样是在第2个边沿，数据发送在第1个边沿


**例如：**
CPOL=0，CPHA=0：此时空闲态时，SCLK处于低电平，数据采样是在第1个边沿，也就是SCLK由低电平到高电平的跳变，所以数据采样是在上升沿，数据发送是在下降沿。

CPOL=0，CPHA=1：此时空闲态时，SCLK处于低电平，数据发送是在第1个边沿，也就是SCLK由低电平到高电平的跳变，所以数据采样是在下降沿，数据发送是在上升沿。

CPOL=1，CPHA=0：此时空闲态时，SCLK处于高电平，数据采集是在第1个边沿，也就是SCLK由高电平到低电平的跳变，所以数据采集是在下降沿，数据发送是在上升沿。

CPOL=1，CPHA=1：此时空闲态时，SCLK处于高电平，数据发送是在第1个边沿，也就是SCLK由高电平到低电平的跳变，所以数据采集是在上升沿，数据发送是在下降沿。
![这里写图片描述](https://img-blog.csdn.net/20180507195849584?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180507195903464?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

需要注意的是：我们的主设备能够控制时钟，因为我们的SPI通信并不像UART或者IIC通信那样有专门的通信周期，有专门的通信起始信号，有专门的通信结束信号；**所以我们的SPI协议能够通过控制时钟信号线，当没有数据交流的时候我们的时钟线要么是保持高电平要么是保持低电平**。
