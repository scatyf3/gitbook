---
description: 汇总了多篇博文，以及教科书和ppt
---

# 计算机网络 物理层笔记

​![image](<.gitbook/assets/image 20231219235637 9hll863.png>)​

对于物理层，我确实不知道应该以什么顺序讨论它——但是根据我的朴素的理解，我们可以把知识根据传输的循序划分为下述部分：

1. 编码
2. 数字信号
3. 调制
4. 模拟信号
5. 发送模拟信号
6. 物理介质
7. 采样/接收
8. ...（又回到模拟信号，所以不再赘述）

## 物理层的例子

物理层的协议主要有如下：\
[USB 协议](https://en.wikipedia.org/wiki/Universal\_Serial\_Bus)\
[蓝牙协议](https://en.wikipedia.org/wiki/List\_of\_Bluetooth\_protocols)的一部分\
[IEEE 802.11](https://en.wikipedia.org/wiki/IEEE\_802.11) 的一部分（Wi-Fi）\
[IEEE 802.16](https://en.wikipedia.org/wiki/IEEE\_802.16)（WiMAX）\
[IEEE 1394](https://en.wikipedia.org/wiki/IEEE\_1394)（火线接口）\
[RS-232 协议](https://en.wikipedia.org/wiki/RS-232)（串行接口/串口）

## 信道和介质

为了对物理层这一层的特色有一个实感，我们先从中间的物理介质开始讨论，这部分我们在现实生活中见到过许多

### 物理介质

在网络的最低层次上,所有计算机通信都是以某种能量形式编码数据通过传输介质传送而实现

#### 导线

\*\*导线具有电磁干扰 ，但是便宜和传输远\*\*

#### 双绞线

**双绞线>>绞在一起可以限制彼此以及阻止其他导线的电磁干扰**

​![b012f1a5abfe1269a16df8645b9ef3ab](<.gitbook/assets/0 20231219182141 3jedmsc.png>)​

#### **同轴电缆**

**同轴电缆 >>** **抗干扰能力更好,由一根被金属屏蔽层包围的导线组成**

​![942c5bf043013d163cab013f97252967](<.gitbook/assets/1 20231219182141 ej02uaj.png>)​

### 信道的工作模式

​![image](<.gitbook/assets/image 20231219183208 lwmzgep.png>)​

　　**单工（simplex）**\
比如“电台广播”就是典型的【单工】。“电台”可以发信号给“收音机”，但“收音机”【不能】发信号给“电台”。

　　**半双工（half-duplex）**\
比如“单条铁路轨道”，就是典型的【半双工】。火车在单条铁轨上，可以有两种运行方向；但对于同一个瞬间，只能选其中一个方向（否则就撞车了）。

　　**全双工（full-duplex）**\
比如“光纤”就是典型的【全双工】。在同一根光导纤维中，可以有多个光束【同时相向】传播，互相不会干扰对方。

### \*\*远距离传输的介质 \*\*

**远距离传输——租用串行数据线路——所以我们需要若干技术尽可能利用它**

​![77a547ab04ea45fa61cbd060e3502fe9](<.gitbook/assets/5 20231219182141 6tvdpgp.png>)​

### 带宽

这是信号在物理世界里传播的限制

\~\~ \~\~​~~**实际上带宽被定义成0到能还有一半能量通过的频率处。【这谁说的】**~~

定义：某个信道在单位时间内最大能传输多少比特的信息

* 计算机网络中：最高速率，单位为b/s（时域）
* 通信：信号的带宽指的是该信号所包含的不同频率成份所占据的频率范围，单位为Hz（每秒钟振动一次为1赫兹，可以写成次/秒）（频域）

其实两者表征的本质是一样的，假如说每个码元代表振动6次，那么6次/秒=b/s，只不过由于在计算机网络中[光纤通信](https://www.zhihu.com/search?q=%E5%85%89%E7%BA%A4%E9%80%9A%E4%BF%A1\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=%7B%22sourceType%22%3A%22article%22%2C%22sourceId%22%3A%22294905494%22%7D)采用的是[激光脉冲信号](https://www.zhihu.com/search?q=%E6%BF%80%E5%85%89%E8%84%89%E5%86%B2%E4%BF%A1%E5%8F%B7\&search\_source=Entity\&hybrid\_search\_source=Entity\&hybrid\_search\_extra=%7B%22sourceType%22%3A%22article%22%2C%22sourceId%22%3A%22294905494%22%7D)，这是一种离散的数字信号，其有别于传统连续的模拟信号，因此不宜用Hz来描述带宽，所以统一用b/s来描述。

​![image](<.gitbook/assets/image 20231219191318 lqtgy1l.png>)​

带宽是介质的固有属性,取决于材料、粗细和长度

### 复用技术

一个物理线路被用来同时传输多路信号的技术，讲述如何更高效的利用物理信道

​![](<.gitbook/assets/6 20231219182141 1bshs6d.png>)​

​​

> TDM 可以看作是所有的人都聚集在大厅里按顺序进行交谈。\
> FDM可以看作是大厅里的人以不同的语调交谈,某些语调高些,某些语调低些,所有的交谈可同时进行并相互独立。CDMA 可以看作是大厅里的每一对交谈使用不同的语言。讲法语的这一对在谈论有关法国的事情,并且把所有与法国无关的内容都当作噪声拒绝掉。

#### **频分多路复用/FDM（Frequency-Division Multiplexing）**

​![image](<.gitbook/assets/image 20231219185344 medx92t.png>)​

​![7d174fe6a2276c2ced07730c67829ac0](<.gitbook/assets/17 20231219182141 wsp7zr6.png>)​

　　这个最简单，就是根据频率拆分。不同的线路占用不同的频段，互不干扰。（电台广播用的就是这个思路）\
但这个思路的缺点很明显——\
其一，要依赖足够宽的频段（频段是稀缺资源）；\
其二，不同线路的流量可能会动态变化。如果某个线路空闲，其占用的频段就浪费了。\
（注：光纤通讯中有个“波分多路复用/WDM”，本质上就是 FDM）

#### 波分多路复用

​![2b1e11072a798c9ce51196c0f9171995](<.gitbook/assets/18 20231219182141 s1rsx94.png>)​

#### **时分多路复用/TDM（Time-Division Multiplexing）**

​​![33bda42271f190ec050e2ed6a286eed6](<.gitbook/assets/19 20231219182141 cbczlvx.png>)​\
这种思路只用一个很窄的频段。为了在同一个频道发送多个信道，采用【分时机制】，把时间切割成很小的时间片，每个线路占用一个时间片。周而往复。\
这个思路有点像十字路口的红绿灯——每隔一段时间，其中一条路可以通行。\
这个思路的优点是：可以只使用一个很窄的频段。缺点是：线路越多，每条线路等待越久；即使某个线路空闲，依然会占用时间片（浪费了资源）。

#### 　　**码分多路复用/CDM（Code-Division Multiplexing）【todo】**

​![image](<.gitbook/assets/image 20231219185516 i21sy4k.png>)\
这种思路采用某种【编码】的技巧，使得多个端点可以在同一个时间点使用同一频段发送数据；由于他们采用不同的编码方式，不会相互干扰。

Code Division Multiple Access allows transmissions to be interleaved, but avoids interference. Note that this means inherently no message collision->多个用户用同一信道通信而不互相干扰

**原理 ：CDMA通过将每个用户的信号编码为唯一的序列来实现这一点，这些序列之间是正交的，因此可以同时传输多个信号。这种技术可以提高频谱利用率，从而使更多的用户能够使用同一频率进行通信。**

示例

​​

### 交换Switching【todo】

#### **传统电话交换**

**Circuit switching: make a true physical connection from sender to receiver. This is what happens in traditional telephone systems**

**这个命名和更早时间的电话交换有关系**

#### \*\*信息交换 \*\*

**Variation: message switching – a message is completely received at a router, stored, and then put into an outgoing queue for further routing.**

#### \*\*包交换 \*\*

**Packet switching:**

**(1)split any data into small packets,**

**(2) route those packets separately from sender to receiver, and**

**(3)assemble them again.**

## 编码

看过物理介质之后，我们还是按顺序介绍物理层。首先，物理层的第一步就是把理论世界的0-1编码为数字信号（高低电平）

### 编码的定义

计算机采用二进制数字表示数据,数据传输就是通过传输介质发送位串。通常,位串被编码成合适的方式以电流、无线电波或光的形式被传送

### **朴素方法**

**使用微小的电流编码数据,比如:负电压表示1,正电压表示0;**

​![e6aff8683c0032c9635baf9565441227](<.gitbook/assets/2 20231219182141 7fuovtn.png>)​

​

### **现实生活 ：EIA的RS-232异步字符传输规程**

* **异步传输 发送器和接收器之间发送数据前无需协调;**
* **没有0v** **因为导线在各位的传送间隙不再回到零状态(归零),接收器不再能从电压的消失来标记一位的结束和另一位的开始。**
* **计时器** **当字符的第1位到达时,接收器启动一个计时器,并且使用该计时器定时测量每一个后续位的电压**
* 串行通信 **如果数据的各位在(可能是多根)导线上逐位传输,则称为串行通信**

\*\*例子 \*\*

​![ea8e3b51d94c21e6ed3673df37a99526](<.gitbook/assets/3 20231219182141 8v4wree.png>)​

​

**停止位 >>** **发送两个字符之间的间隙至少是传输一位所需的时间(那就是说传输一个字符相当于需要9位的传输时间),此位也称为停止位(stop bit)**

**起始位 >>** **发送器在传输字符的各位之前先传输一位额外的0,称为起始位(start bit);**

**缺陷 >>** **不能用于远距离通信**

‍

## 调制

若是要长距离通信，我们需要用到电磁介质和模拟信号

### 理论基础

信号长距离传输的理论基础： **一个连续振荡信号能比其它信号传播到更远的地方**

通过傅立叶变换，用模拟信号表示数据信号【不用了解细节】

​![39d8655c875ffb7e0b94d0a2f36c5d99](<.gitbook/assets/9 20231219182141 yorkxvt.png>)​

### \*\*载波和调制 \*\*

**这个振荡信号(通常为正弦波)可用于“承运”待发送的数据,故又称为“载波(carrier)”,如何承运的方法称为调制。即:为了发送数据, 发送装置将修改其载波,这种修改称为调制(modulation)**

【可以理解为传输效率in real life】

### 三种调制技术

* **幅移调控 >>** **Use different frequencies to encode your bits (these frequencies can be put “on top” of your base frequency).**
* **频移调控 >>** **Change the amplitude (strength) of the signal: changing amplitude means a binary 1, constant amplitude a binary 0.**
* **相移调控 >>** **Change the phase of the wave (cf. sine and cosine) to do signal encoding**

**示例图 >>**

​![082440f50ac2f6e2bd201463fa3d5602](<.gitbook/assets/13 20231219182141 9yzvuqu.png>)​

‍

**irl的工程问题 >>** **实践中,大多数Modem采样频率2400Hz,焦点在于:每次采样如何表达更多的位信息**

### 调制技术的\*\*综合使用：星座图 \*\*

​![ffe8bf61849d2ff339aed9ee267bb0f3](<.gitbook/assets/14 20231219182141 7pmfr5n.png>)​

#### 一个当时的总结

卧槽，我好像明白这个意思了 我觉得这里其实是理解整个物理层的核心。理论上我们在物理层传输的是二进制数字信号，但是现实生活中存在的只有模拟信号，所以我们通过对模拟信号的波长，振幅，相位进行调制，往里面尽可能的塞信息。

但是因为通信的带宽，采样的频率，噪音的干扰等原因，我们不可能无限的塞信息。

这就是全部物理层的核心知识？🤔

### 调制技术的使用例

Asymmetric Digital Subscriber Line（ADSL）是一种数字用户线（DSL）技术，用于在标准电话线上提供高速数据传输。ADSL使用了一种称为频分多路复用（Frequency Division Multiplexing，FDM）的技术，允许将电话信号和数据信号同时传输在同一条电话线上，而不会相互干扰。

‍

​![4c00872ae19cc6d502061c61f2ffc517](<.gitbook/assets/15 20231219182141 ycp654k.png>)​

‍

‍

​![65a92a929ba86c60553418bc85a0ad81](<.gitbook/assets/16 20231219182141 q5zdhft.png>)​

***

### 衰减，畸变，过滤器

传输介质存在衰减和畸变 >> 所以我们引入过滤器

​![133bdcda8b5ff64244da38bd97738e99](<.gitbook/assets/10 20231219182141 lobp4eh.png>)​

## 传输效率-物理介质的传输和采样

### 前置知识：信息的度量

$$
H = nlogs
$$

* n为number of symbol，如果在一秒钟的话，这个就是波特率
* s为symbol rate，形容在一个symbol里有多少信息

### 熵

$$
H=-\sum_{i=1}^{n} p_i \times \log _{2}(p_i)
$$

### \*\*Nyquist定律 \*\*

$$
maximum-transmision-rate = 2(Hlog_2V) bps
$$

奈奎斯特采样定理解释了采样率和所测信号频率之间的关系，即**采样率fs必须大于被测信号最高频率fN的2倍**

​​![image](<.gitbook/assets/image 20231219191704 uykxfuj.png>)

如果采样的频率更低，则信号里会出现虚假的低频部分

​​

‍

### 分贝的含义

P1表示感兴趣的物理量的功率或幅度，P0表示参考物理量的功率或幅度。分贝可以是正数或负数，取决于P1相对于P0是增加还是减小

$$
dB = 10 * log_{10}(P_1/P_0)
$$

### \*\*香农定律 \*\*

$$
C=Blog_2(1+S/N)
$$

​

### 两个公式的关系

我的理解是，**Nyquist定律描述的是物理上的通信规律限制，香农定律描述的数学上的编码信息量的限制，两者处于两个层面**

> 4、如果在一条3kHz的信道发送一个二进制信号，该信道的信噪比为20dB，试问可达到的最大数据率为多少？\
> 解：信噪比20dB，则S/N=100。由香农定理最大速率=B log2（1+S/N) = 3 \*log2 (101) = 19.975kbps.但是尼奎斯特定律的限制是2H = 6kbps 所以实际上最大速率6kbps。

‍
