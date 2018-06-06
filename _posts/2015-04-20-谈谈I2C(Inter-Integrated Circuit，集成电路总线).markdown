---
title:  "谈谈I2C(Inter-Integrated Circuit，集成电路总线)"
subtitle: "The Fly!"
author: "Wferr"
avatar: "img/authors/wferr.png"
image: "img/a.jpg"
date:   2015-04-20 12:12:12
---

### 谈谈I2C(Inter-Integrated Circuit，集成电路总线)
今天我们来一起聊聊I2C(Inter-Integrated Circuit，集成电路总线) ；
首先抛出一个问题吧！
我们已经有了及其方便的UART(Universal Asynchronous Receiver and Transmitter)数据通信协议了，为什么还要有I2C、SPI这些协议呢？


----------

本文对I2C进行一个简单的知识普及；
------------------

I2C(Inter-Integrated Circuit，集成电路总线)：I2C总线是由Philips公司开发的一种简单、双向二线制同步串行总线。它只需要两根线即可在连接于总线上的器件之间传送信息。
他拥有2根线：SDA（串行数据线）和SCL（串行时钟线）；
![这里写图片描述](https://img-blog.csdn.net/20180504143324706?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
SDA（串行数据线）和SCL（串行时钟线）都是双向I/O线，接口电路为开漏输出．需通过上拉电阻接电源VCC．当总线空闲时．两根线都是高电平，连接总线的外同器件都是CMOS器件，输出级也是开漏电路．在总线上消耗的电流很小，因此，总线上扩展的器件数量主要由电容负载来决定，因为每个器件的总线接口都有一定的等效电容．而线路中电容会影响总线传输速度．当电容过大时，有可能造成传输错误．所以，其负载能力为400pF，因此可以估算出总线允许长度和所接器件数量。


----------


一般CPU都是自带I2C总线功能，因此在硬件角度使用起来是非常简单的；
I2C总线只需要一根数据线和一根时钟线两根线，总线接口已经集成在芯片内部，不需要特殊的接口电路，而且片上接口电路的滤波器可以滤去总线数据上的毛刺．因此I2C总线简化了硬件电路PCB布线，降低了系统成本，提高了系统可靠性。因为12C芯片除了这两根线和少量中断线，与系统再没有连接的线，用户常用IC可以很容易形成标准化和模块化，便于重复利用。
下图为335X，一个CORTEX-A8的ARM；带有3路的I2C，总线接口已经集成在芯片内部，不需要特殊的接口电路；
![这里写图片描述](https://img-blog.csdn.net/20180504143527918?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


----------

使用指南
----
![这里写图片描述](https://img-blog.csdn.net/20180504143814420?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![这里写图片描述](https://img-blog.csdn.net/20180504144306363?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

直接从CPU中引出的I2C的两条线（SDA和SCL）分别经过一个上拉电阻后直接用在一个256K*8的EEPROM中去；


I2C相对于其他的协议来说，还有一个优点就是：   
I2C总线是双向、两线(SCL、SDA)、串行、多主控（multi-master）接口标准，具有总线仲裁机制，非常适合在器件之间进行近距离、非经常性的数据通信。在它的协议体系中，传输数据时都会带上目的设备的设备地址，因此可以实现设备组网。
它支持多主控(multimastering)，其中任何能够进行发送和接收的设备都可以成为主总线。一个主控能够控制信号的传输和时钟频率。当然，在任何时间点上只能有一个主控。
![这里写图片描述](https://img-blog.csdn.net/2018050414473266?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


----------

另附一个I2C时序图
----------
![这里写图片描述](https://img-blog.csdn.net/2018050414520110?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

1、总线空闲状态 ：
		因为I2C总线的两条信号线，均有一个上拉电阻把电平拉高；默认状态下是一个高电平，因此I2C总线总线的SDA和SCL两条信号线同时处于高电平时，规定为总线的空闲状态。此时各个器件的输出级场效应管均处在截止状态，即释放总线。
		
2、启动信号 
　　在时钟线SCL保持高电平期间，数据线SDA上的电平被拉低（即负跳变），定义为I2C总线总线的启动信号，它标志着一次数据传输的开始。启动信号是一种电平跳变时序信号，而不是一个电平信号。启动信号是由主控器主动建立的，在建立该信号之前I2C总线必须处于空闲状态。

3、重启动信号 
　　在主控器控制总线期间完成了一次数据通信（发送或接收）之后，如果想继续占用总线再进行一次数据通信（发送或接收），而又不释放总线，就需要利用重启动Sr信号时序。重启动信号Sr既作为前一次数据传输的结束，又作为后一次数据传输的开始。利用重启动信号的优点是，在前后两次通信之间主控器不需要释放总线，这样就不会丢失总线的控制权，即不让其他主器件节点抢占总线。
 
4、停止信号 
　　在时钟线SCL保持高电平期间，数据线SDA被释放，使得SDA返回高电平（即正跳变），称为I2C总线的停止信号，它标志着一次数据传输的终止。停止信号也是一种电平跳变时序信号，而不是一个电平信号，停止信号也是由主控器主动建立的，建立该信号之后，I2C总线将返回空闲状态。


----------

附：总线竞争的仲裁 
--
   总线上可能挂接有多个器件，有时会发生两个或多个主器件同时想占用总线的情况，这种情况叫做总线竞争。I2C总线具有多主控能力，可以对发生在SDA线上的总线竞争进行仲裁，其仲裁原则是这样的：当多个主器件同时想占用总线时，如果某个主器件发送高电平，而另一个主器件发送低电平，则发送电平与此时SDA总线电平不符的那个器件将自动关闭其输出级。总线竞争的仲裁是在两个层次上进行的。首先是地址位的比较，如果主器件寻址同一个从器件，则进入数据位的比较，从而确保了竞争仲裁的可靠性。由于是利用I2C总线上的信息进行仲裁，因此不会造成信息的丢失。
   
    
欢迎关注本人公众号：
----------

![这里写图片描述](https://img-blog.csdn.net/20180605163047559?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEyNDg4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


AM335x硬件开发使用指南（三） 
https://mp.weixin.qq.com/s/LHOcr66ktBisaYdORG-2cQ
https://blog.csdn.net/weixin_42124889/article/details/80571506

AMR335x硬件开发使用指南（二） 
https://mp.weixin.qq.com/s/krjhaz7LvXxiT2a9pvnAFw

AM335x硬件开发使用指南（一） 
https://blog.csdn.net/weixin_42124889/article/details/80514842



聊聊无线充电——华为Mate RS保时捷版无线充电器详解（上篇） 
https://blog.csdn.net/weixin_42124889/article/details/80290757

聊聊无线充电——华为Mate RS保时捷版无线充电器详解（下篇） 
https://mp.weixin.qq.com/s/9o8-7YANXCiydjQ8u-EDsg

手机快充技术——快充技术&硬件详解 
https://blog.csdn.net/weixin_42124889/article/details/80255434

一文知晓高通晓龙845 
https://mp.weixin.qq.com/s?__biz=MzU2NjYwMzQ1Ng==&mid=2247483678&idx=1&sn=c396ca9582a7f5124e0b00cb94d6a482&chksm=fca8bab1cbdf33a79189346c9ed964bca08f446427f67a509b3577bec2fd533fe6aeb39d5eaf#rd

A12 chip即将来临，是IPhone XI？ 
https://mp.weixin.qq.com/s/j3IJdhY1CKS7aW120zgs8Q