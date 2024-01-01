# 计算机网络 链路层笔记

## 计算机网络 链路层笔记

## 链路层的主要任务

a) 成帧 >>\
b) 数据接入 >>\
c) 可靠交付 >>\
d) 差错控制 >>

## 链路层在现实生活中

### 个人电脑

链路层的主体部分是在**网络适配器**中实现的,网络适配器有时也称为**网络接口卡**。位于网络适配器核心的是链路层控制器,该控制器通常是一个实现了许多链路层服务(成帧、链路接入、差错检测等)的专用芯片

​![image](<.gitbook/assets/image 20231221113006 go09gth.png>)​

### 通信设施

网络交换机：交换机是工作在2层（链路层）的设备，能够理解链路层协议。当交换机从某个网线接口收到一份数据（链路层的“帧”），它可以识别出“链路帧”里面包含的目标地址（接收方的 MAC 地址），然后只把这份数据转发给“目标 MAC 地址相关的网线接口”，【链接同种网络设备】

网桥/桥接机：链接两个不同网络类型的电脑，比如虚拟机通过网桥链接本机？

### 相关工具

* **嗅探抓包工具（Sniffer）**
* **ARP 命令：“MAC 地址解析协议”**

## **对信息的打包-成帧**　　

​![image](<.gitbook/assets/image 20231221113039 x1k1wsi.png>)​

物理层传输的信息，通俗地说就是【比特流】（也就是一长串比特）。但是对于计算机来说，“比特流”太低级啦，处理起来极不方便。“链路层”要干的第一个事情，**就是把“比特流”打包成更大的一坨，以方便更上层的协议进行处理。在 OSI 模型中，链路层的一坨，称之为“帧”（frame）** 。

### 字符计数

​![](<.gitbook/assets/20180109132814410 20231221114030 7lghtbz.png>)​

用帧的第一个字节说明帧的总长度

​![](<.gitbook/assets/20180109133051770 20231221114030 2tjcenv.png>)​

但若是一个帧出错，则后面都会出错，不常用

### bytes填充

​![image](<.gitbook/assets/image 20231221114622 cy6m2k4.png>)​

通过两个flag标志帧头和帧尾，通过esc转义

【需要记忆flag和esc的二进制到底是啥样吗】

### bits填充

​![](<.gitbook/assets/20180109134027670 20231221114030 i4oo7iq.png>)​

这种方法和第二种比较类似，区别是他把flag具体化了，为6个1。这样当正文读取的时候一旦出现了5个连续的1，那么在后面填充一个0，避免出现6个1造成帧提前结束。

## **差错控制**

物理介质的传输，可能受到环境的影响。这种影响不仅仅体现为“噪声”，有时候会出现严重的干扰，导致物理层传输的“比特流”出错（某个比特“从0变1”或“从1变0”）。因此，链路层还需要负责检查物理层的传输是否出错。在 IT 行话中，检测是否出错，称之为“差错控制机制”（后面有一个小节会简单说一下这个话题）。

### 检错编码-算法

常见的“检错算法”对传输的数据计算出一个【校验值】，接收方收到数据会重新计算校验和，如果算出来不对，就把收到的数据丢弃，让对方重发。

#### 奇偶校验

奇偶校验码由N-1位信息元和1位校验元组成。N-1位的信息元就是我们发送信息里的有效数据，而1位校验元就是用于检错的冗余码。

奇偶校验码可以分为奇校验码和偶校验码：

* 奇校验：这串序列1的个数如果为偶数则在前面加个1，使1的个数变成奇数，否则加0。
* 偶校验：这串序列1的个数如果为奇数则在前面加个1，使1的个数变成偶数，否则加0。

​![image](<.gitbook/assets/image 20231221115442 6dtf6kw.png>)​

#### CRC冗余校验

CRC循环冗余检验码是一种通过多项式除法检测错误的方法，将每个比特串看作一个多项式，采用模2运算，具体细节看这篇文章

Cyclic Redundancy Check and Modulo 2 Division

​![image](<.gitbook/assets/image 20231221120225 6rblwa8.png>)​

【todo】CRC冗余校验的局限性\
​![image](<.gitbook/assets/image 20231221212342 m8lokfx.png>)​

### 纠错编码-算法

#### 海明距离

Definition: 海明距离指两个帧同样位置上,取值不同的位置数目。\
Example: 10001001 and 10110001海明距离是3

​![image](<.gitbook/assets/image 20231221120401 t6zw6ef.png>)​

【可以计算长度不同的编码的海明距离-by GPT 不保真】如果两个编码长度不同，可以通过在较短编码的末尾添加空位（通常是0）或在较长编码的末尾截断来对齐它们的长度。这样，两个编码就可以按照相同的长度进行比较，从而计算它们之间的海明距离

#### 海明码【todo】

Essence: every bit at position 2^k^,k>=0 is used as a parity bit for those positions to which it contributes

如何编码：

​![image](<.gitbook/assets/image 20231221120842 m76a8ff.png>)​

【为什么H3在7这里？不是8吗->就是8，它写错了】

如何纠错：

​![image](<.gitbook/assets/image 20231221121138 wttx1ly.png>)​

【本质是通过一系列海明编码，然后找和自己海明距离最近的有效编码，从而完成纠错】

奇怪的问题：如果给你一串二进制值，你怎么确定哪个是高位😭按照十进制的习惯是高位在左，但是按照汉明码的上述计算结构，高位在右\
\->根据习惯约定，给你的二进制位串高位在左，需要你手动将之转换为适合计算的格式

**如果数据不满，应该怎么做？比如1101 0011 0011 0101，5位校验位和11位数据位**

## **流量控制**

　　假设两个端点通过同一个物理信道进行通讯，这两个端点处理信息的速度可能不同。如果发送方输出信息的速度超过接收方处理信息的速度，通讯就会出问题。于是就需要有某种机制来协调，确保发送方的发送速度不会超出接收方的处理速度。在技术行话中，这称之为“流量控制”，简称“流控”。　　

数据链路层的流量控制方法主要是停止等待协议和滑动窗口协议，滑动窗口协议还包括后退N帧协议GBN和选择重协议SR。

【TODO: 这部分可能还要看一下协议原型和书上的代码，以及各种差错情况分析】

### 滑动窗口的概念【TODO】

这部分不太明白，到时候找一篇文章再看看吧

### 停等协议

停止等待协议就是每发送完一个帧就停止发送，等待对方的确认帧，在收到确认帧后再发送下一个帧

​​

#### 无差错

当发送方向接收方发送数据帧，发送方发送0帧到接收方，接收方收到0帧后，会向发送方回复0帧的确认帧ACK 0，发送方收到回复的确认帧ACK 0后，然后再向接收方发送1帧，发送双方不断的进行发送和确认的操作而传输数据。

【问题来了，为啥只有0～1】

* （1）超时计时器设置的重传时间应当比帧传输的平均RTT更长一些。
* （2）发送方发完一个帧后，必须保留该帧的副本，如果没有收到回复帧，则立刻重传该帧。

#### 发送方帧丢失

​​

即发送方**在等待确认帧时**超时重传

#### 接收方确认帧丢失

​​

也是超时重传

### GBN 后退N帧协议

​![image](<.gitbook/assets/image 20231221164245 94rrhoy.png>)​

​​

#### GBN发送方需要响应的三件事

　　（1）上层的调用

　　上层要发送数据时，发送方先检查发送窗口是否已满，如果未满，则产生一个帧并将其发送，如果窗口已满，发送方只需将数据返回给上层，提示上层窗口已满，上层等一会再发送。（实际情况是发送方先缓存这些数据，窗口空闲时再发送）

　　（2）收到了一个ACK帧

　　GBN协议中，对n帧的确认采用**累积确认**的方式，标明接收方已经收到n帧和它之前的全部帧。同时，这里的确认是**捎带确认**。

　　（3）超时

　　与停止-等待协议一样，如果出现超时，发送方重传所有已发送但未被确认的帧。

#### GBN接收方需要响应的事

　　如果正确收到n号帧，并且按序，那么接收方为n帧发送一个ACK确认帧，并将帧中的数据部分交付给上层。其余情况都丢弃帧，并为最近按序接收的帧重新发送ACK，接收方无需缓存任何失序帧，只需要维护一个信息，就是下一个按序接收的帧序号。意思是不是按序收到的帧都会被丢弃，并且不会回复确认帧。

#### **GBN协议总结**

　　（1）累积确认：就是当接收方向发送方发出第n帧的确认信息，那么接收方就认为第n帧之前的所有帧接收方都已正确接收。

　　（2）接收方只按顺序接收帧，不按顺序会被丢弃。接收方有一个计录需要接收帧的序号，如果不是它都不会接收直接丢弃。

　　（3）发送方窗口最大为 2^n^ - 1，接收窗口大小为1。

#### GBN常见问题

**为什么最大窗口尺寸是2**​^**n**^​ \*\* - 1?\*\*\
为了看清楚为什么必须有这个限制,请考虑下面 MAX\_SEQ=7 的场景:\
(1) 发送方发送`0~7`​号共8个帧。\
(2) 7号帧的捎带确认返回到发送方。\
(3)发送方发送另外8个帧,其序列号仍然是`0~7`​。\
(4) 现在7号帧的另一个捎带确认也返回了。\
问题出现了:属于第二批的8个帧全部成功到达了呢,还是这8个帧全部丢失了(把出错之后丢弃的帧也算作丢失)?在这两种情况下,接收方都会发送针对7号帧的确认,但发送方却无从分辨。由于这个原因,未确认帧的最大数目必须限制为不能超过 MAX\_SEQ。

**每个未确认的帧都需要一个定时器，我们怎么获得多个定时器？**\
一个硬件时钟和一个软件链表; 图里的剩余滴答数是时间差而不是时间\
​![image](<.gitbook/assets/image 20231221170336 umyyumi.png>)​

**【要考】如果没有反向流量以捎带确认怎么办?**\
当一个按正常次序发送的数据帧到达之后,接收方通过start-ack-timer启动一个辅助的定时器。若在定期器到期之前没有反向流量,则发送一个单独的确认帧。

#### GBN协议性能分析

因连续发送数据帧而提高了信道利用率（与停止-等待协议比较），但是在重传时把原来已经正确传送的数据帧重传，又降低了效率。

### 选择重传协议

​​

为解决后退N帧协议GBN数据帧丢失需要重传确认的问题，选择重传协议SR解决问题的办法是设置单个确认，同时加大接收窗口，设置接收缓存（缓存乱序帧）

#### 　　SR发送方需要响应的三件事

　　（1）上层的调用

　　上层要发送数据时，SR发送方先检查发送窗口可用于该帧的序号，如果序号位地发送窗口内，则发送数据帧；否则就像GBN一样，要么将数据缓存，要么返回上层之后再传输。

　　（2）收到了一个ACK帧

　　如果收到ACK，加入该帧序号在窗口内，则SR发送方将被确认的帧标记为已接收，如果该帧序号是窗口的下界（最左边第一个窗口对应的序号），则窗口向前移动到具有最小序号的未确认帧，如果窗口移动到有序号并未发送的帧，则发送帧。

　　（3）超时

　　每个帧都有自己的定时器，当某帧确认超时，则只重传某帧。

#### 　　SR接收方需要响应的事

　　来者不拒（窗口内的帧）

　　SR接收方将确认一个正确接收的帧而不管其是否按序，失序的帧将被缓存，并反回给发送方一个该帧的确认帧，当序号最小的帧被接收，这时可以将一批按序帧交付给上层，则向前移动滑动窗口。图例：

​​

‍

## 链路层协议 in real life

链路层的协议主要有如下：\
[MAC 协议](https://en.wikipedia.org/wiki/Media\_access\_control)（介质访问控制）\
[LLC 协议](https://en.wikipedia.org/wiki/Logical\_link\_control)（逻辑链路控制）\
[ARP 协议](https://en.wikipedia.org/wiki/Address\_Resolution\_Protocol)（解析 MAC 地址）\
[IEEE 802.3](https://en.wikipedia.org/wiki/IEEE\_802.3)（以太网）\
[IEEE 802.11](https://en.wikipedia.org/wiki/IEEE\_802.11) 的一部分（Wi-Fi）\
[L2TP 协议](https://en.wikipedia.org/wiki/Layer\_2\_Tunneling\_Protocol)（2层VPN）\
[PPP 协议](https://en.wikipedia.org/wiki/Point-to-Point\_Protocol)（拨号上网）\
[SLIP 协议](https://en.wikipedia.org/wiki/Serial\_Line\_Internet\_Protocol)（拨号上网）

### HDLC协议

【等等，这个协议连自顶向下方法的教科书上都未曾出现过😭】

细节的解析可以参考这篇文章，这里只是快速的过一下ppt里的内容

​![image](<.gitbook/assets/image 20231221185247 srosobp.png>)​

【成帧】采用**零比特插入法**，易于硬件实现，且支持任意的位流传输，实现信息的透明传输（**bits填充**）

【差错控制】采用**CRC帧校验序列**，可防止漏帧，提高信息传输的可靠性

【流量控制】全双工通信，吞吐率高，在未收到应答帧的情况下，可连续发送信息帧，提高数据链路传输的效率（是GBN还是SR呢？->两种都可以🤔通过control字段控制）

control字段里还有：sequence numbers, acks, nacks

### PPP协议

【ppp基于HDLC协议】PPP encapsulation is based on the High-level Data Link Control (HDLC) derived from the mainframe environment.

​![image](<.gitbook/assets/image 20231221190037 dg4b5pw.png>)​

​![image](<.gitbook/assets/image 20231221190743 uk1oq26.png>)​

它通常用在两[节点](https://zh.wikipedia.org/wiki/%E8%8A%82%E7%82%B9\_\(%E7%94%B5%E4%BF%A1%E7%BD%91%E7%BB%9C\)) "节点 (电信网络)")间建立直接的连接，并可以提供连接[认证](https://zh.wikipedia.org/wiki/%E8%BA%AB%E4%BB%BD%E9%AA%8C%E8%AF%81)、传输[加密](https://zh.wikipedia.org/wiki/%E5%8A%A0%E5%AF%86)以及[压缩](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E5%8E%8B%E7%BC%A9)。

PPP被**用在许多类型的物理网络**中，包括[串口](https://zh.wikipedia.org/wiki/%E4%B8%B2%E5%8F%A3)线、[电话线](https://zh.wikipedia.org/wiki/%E9%9B%BB%E8%A9%B1%E7%B7%9A)、[中继链接](https://zh.wikipedia.org/wiki/%E4%B8%AD%E7%B9%BC%E9%8F%88%E6%8E%A5)、[移动电话](https://zh.wikipedia.org/wiki/%E7%A7%BB%E5%8A%A8%E7%94%B5%E8%AF%9D)、特殊无线电链路以及光纤链路（如[SONET](https://zh.wikipedia.org/wiki/SONET)）。

PPP**还用在**​[**互联网**](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91)​**接入上**。早年，[互联网服务提供商](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E6%9C%8D%E5%8A%A1%E6%8F%90%E4%BE%9B%E5%95%86)（ISP）使用PPP为用户提供到[Internet](https://zh.wikipedia.org/wiki/Internet)的[拨号连接](https://zh.wikipedia.org/wiki/%E6%8B%A8%E5%8F%B7%E8%BF%9E%E6%8E%A5)，这是因为IP报文无法在没有数据链路协议的情况下通过[调制解调器](https://zh.wikipedia.org/wiki/%E8%B0%83%E5%88%B6%E8%A7%A3%E8%B0%83%E5%99%A8)线路自行传输。PPP的两个派生物[PPPoE](https://zh.wikipedia.org/wiki/PPPoE)和[PPPoA](https://zh.wikipedia.org/wiki/PPPoA)被ISP广泛用来与用户建立[数字用户线路](https://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%97%E7%94%A8%E6%88%B7%E7%BA%BF%E8%B7%AF)（DSL）、[FTTx](https://zh.wikipedia.org/wiki/FTTx)互联网服务连接。

​![image](<.gitbook/assets/image 20231221191145 qb3m3un.png>)​

#### 协议格式

​![image](<.gitbook/assets/image 20231221190233 fp1jpee.png>)​

【成帧】多协议成帧？->protocol字段

【差错控制】Frame Check Sequence。在常见的以太网协议中，FCS使用循环冗余检验（Cyclic Redundancy Check，CRC）算法进行计算

【流量控制】[链路控制协议](https://zh.wikipedia.org/wiki/%E9%93%BE%E8%B7%AF%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE)和[网络控制协议](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE)这两个子协议

#### 链接流程

假设您想要与您的提供商建立真正的互联网连接。\
◼ 通过调制解调器建立物理连接。\
• Pc通过Modem呼叫提供商先收到答复，建立物理连接\
◼ 您的 PC 开始发送大量链路控制数据包 (LCP)，以协商您想要的 PPP 连接类型。\
请注意，这些数据包嵌入在 PPP 帧中： • 数据帧中的最大有效负载大小 • 进行身份验证（例如要求密码） • 监控链路质量（例如有多少帧未通过）。\
• 压缩标头（对于快速计算机之间的慢速链接很有用）\
◼ 然后，我们协商网络层内容，例如获取提供商的路由器可用于将数据包转发给您的 IP 地址。 // NCP，网络控制协议，破坏了层间关系。

【先协商链接类型，再协商上层内容】

​![image](<.gitbook/assets/image 20231223201316 i5fwocp.png>)​

#### PPP and DHAP

【todo：这部分啥意思...】

•如果IP地址是动态分配的，谁来分配？ 提供者。\
•如果IP地址是动态分配的，其他人是否可以向您发送数据( 他们不知道您的地址，是吗？我们需要先联系他们（我们的地址包含在请求中）。

## **信道复用**

　　在上一个章节已经讲到：用于远距离通讯的“物理介质”，总是有成本。因此需要对物理信道进行“多路复用”，就会导致多个端点共用同一个物理信道。如果同时存在多个发送者和多个接收者。接收者如何知道某个信息是发给自己而不是别人？\
另外，某些物理介质可能不支持并发（无法同时发送信息）。某些物理介质可能是【半双工】，所有这些物理层的限制，都使得“多路复用”变得复杂。为了解决这些问题，链路层需要提供了某种相应的机制（协议），术语叫做“介质访问控制”（洋文是“Media Access Control”，简称 MAC）。

我们可以把这种信道控制划分为三个策略：

* 完全不控制：简单地让站点尝试使用信道，并在发生冲突时执行某些操作。应用于竞争系统。
* 采用循环技术：允许每个站轮流使用该信道。应用于基于令牌的系统——拥有令牌的站可以使用该信道。
* 让电台预留频道。用于时隙系统。问题是如何进行预留。
  * 事实上，我们已经实现了部分信道复用，比如之前的x分多路复用就是一种静态信道复用，但是它的效率还不够高。

策略选择：我们真正想要的是**轻载时的争用策略，以及高峰时段的无碰撞策略**\
现实生活中：CSMA/CD适用于有线网络，CSMA/CA适用于无线网络

### ALOHA系统

#### 简单的系统

Principle： 如果想发就发好了。如果发生碰撞，等待一段随机事件后重试

**效率分析**

在t~~0~~时刻发送帧，会与在\[t~~0~~-1，t~~0~~+1]期间其他结点发送的帧冲突\
​\
P(给定结点成功发送帧) = P(该结点发送) \* P(无其他结点在\[t~~0~~-1，t~~0~~]期间发送帧) \* P(无其他结点在\[t~~0~~，t~~0~~+1]期间发送帧)\
\= p \* (1-p)^N-1^ \* (1-p)^N-1^\
\= p \* (1-p)^2(N-1)^\
\= 1/(2e) = 0.18（选取最优的p，并令n）

#### 升级版：分槽ALOHA

​​

【通过某些神秘的数学计算，我们得知分槽aloha性能会好很多】

**原理**

所有帧大小相同，时间被划分为等长的时隙(每个时隙可以传输1个帧)，结点只能在时隙开始时刻发送帧，结点间时钟同步，如果2个或2个以上结点在\
同一时隙发送帧，结点即检测到冲突。

当结点有新的帧时，在下一个时隙(slot)发送，如果无冲突，该结点可以在下一个时隙继续发送新的帧，如果冲突，该结点在下一个时隙以概率p重传该帧，直至成功。

​​

**效率**

一个给定的结点成功传送的概率是p(1-p)^{n-1}^， 因为有N个结点，任意一个结点成功传送的概率是Np(1-p)^{n-1}^。当活跃结点数量趋向无穷大时，最大效率1/e，即37%。

【挺重要的，我猜测要考】

### CDMA

这个比ALOHA好，因为它在发送之前就检测冲突

#### 分类

* 1-persistent: listen whether the channel is free before transmitting. 当信道忙的时候，持续监听直到空闲便开始发送。
* Nopersistent： Less greedy - 当信道忙的时候，等待随机时间再来尝试.If you wait to long, the channel utilization drops.
* P-Persistent：用于分（时）槽系统.若发现当前时槽信道空闲，以概率p使用之。
  * P=1 is not really good, P=0 makes you really polite.

​​

Question: Should the conclusion be that p- persistent protocos are really qood with p=0？\
p=0不是大家太谦虚，然后没人用信道了吗

#### 碰撞检测

但是在现实生活中，检测有没有节点使用这个信道是一个工程难题，因为我们有传播时延等等一众延迟

改进\
• 在传输过程中，保持监听以检测冲突。如果发生冲突，传输立即停止。\
• 如果发生冲突，则等待一段随机时间，然后再次继续第一步。

​![3801703746949\_.pic](<.gitbook/assets/3801703746949\_.pic 20231228150246 g6lhhko.jpg>)​

知道无冲突的最短时间是$2T-\delta$，这被称为竞争期/征用期

### 免于冲突的协议

#### 位图

这是一类免于冲突的协议

1. 如有 N 个站点共享信道，编号为 0\~N-1
2. 竞争周期将分为 N 个时隙，**每个站点占有一个时隙，如某站准备发送，则可在属于它的时隙内填入 1**
3. 一个竞争周期后，则将按顺序发送，不会产生冲突

​​

简而言之，就是在**竞争期**轮流修改位图，表示自己在下一个传输期有无意愿传输数据

#### 二进制倒数

为了避免冲突,必须使用一条仲裁规则:一个站只要看到自己的地址位中的0值位置被改写成了 1,则它必须放弃竞争

即通过异或运算和判断01，决定要不要发送

​​

### 有限竞争协议

【怎么选择协议】\
• 当没有发生太多事情时，争用系统是好的 - 站点可以立即传输帧。\
当出现问题时我们会进行一些修复。\
• 当通常存在大量流量时，无冲突系统是很好的——站点首先必须在帧传输之前明确地获取信道。 我们做了很多工作来避免碰撞。\
我们真正想要的是**轻载时的争用策略，以及高峰时段的无碰撞策略**

所以我们综合上述两种策略\
解决方案：在竞争期间动态调节竞争站的数量。\
如果在第 k 个时段发生冲突，则将竞争者分为两组。\
• 第一组可以在下一个时隙（k+1）再次尝试。 如果没有发生冲突，则第二组在之后的时隙（k+2）内进行尝试。 否则，第一组将再次分裂。\
注意：如果流量不大，则立即允许第一个站发送帧。 对于大量流量，该策略简化为位图协议。

### 自适应树搜索协议

某个竞争时槽，发生冲突，则缩小下一个时槽参与竞争的站点数目；\
不发生冲突，那么下一个时槽应该让另一半节点来竞争；\
当负载较重，显然不应该从树根节点开始尝试•负载越重，越应该从树的下面节点开始搜索

​​

### WDMA

【为啥突然出现了一个光相关的东西，而且还和物理层有关🤔直接截图当展位符吧，懒得看了】

​​

​​

### 以太网

物理层 • 以太网布线 • 曼彻斯特编码\
数据链路层\
• 以太网 MAC 子层协议 - CSMA/CD • 二进制指数退避算法\
• IEEE 802.2：逻辑链路控制子层\
以太网性能\
其他以太网 • 交换式以太网 • 快速以太网 • 千兆以太网

以太网代表 IEEE 802.3 协议的近似实现。 它基于 CSMA/CD（感知通道，等待空闲，并在检测到冲突时退避）

#### 物理接线

这部分也是看看就好...

​​

​​

#### 曼彻斯特编码

接收方在**没有外部时钟参考**的情况下,可以毫无歧义地确定每一位的起始、结束或者中间位置

​​

【注意，差分曼彻斯特编码中，第一个位置没有实际意义】

​​

#### 帧格式

​​

​![](https://img.sysnettechsolutions.com/What-is-Ethernet-Nedir-01.png)​

802.3 帧的组成：

* 前导码：7 个 `10101010`​
* 帧起始符：`10101011`​
* 目的地址（物理地址）
* 源地址（物理地址）
* **长度：不考虑前导码和帧起始符的长度。最短为 64 字节，最长为 1518 字节。**
* 数据：LLC 数据，至少要 46 个字节，如果不够就要填充。
* 校验和：CRC 循环冗余校验，校验范围为除前导码和帧起始符外的字段

Ethernet 帧的组成：

* 前导码：8 个 `10101010`​
* 目的地址（物理地址）
* 源地址（物理地址）
* **类型：上层网络层使用的协议**
* 数据：LLC 数据，至少要 46 个字节，如果不够就要填充。
* 校验和：32 位 CRC 循环冗余校验，校验范围为除前导码和帧起始符以外的字段

为什么有一个字段既要表示类型，也要表示长度？\
是历史遗留问题，因为在IEEE标准出来之前，有很多MAC层协议，这一部分一般由之前的ppp协议里的llc负责，所以我们向既有事实妥协\
“幸运的是,所有在 1997年之前使用的类型字段其值都大于 1500,因此规定了最大数据长度。**现在的规则是,任何值小于或等于 Ox600(1536)可解释为长度字段,任何大于 Ox600可解释为类型字段。** 现在IEEE 可以认为每个人都使用了它的标准, 并且其他人可以继续做他们正在做的事情(不能打扰 LLC),它因而无须感到内疚。“（自顶向下方法原文）

【考试内容】为什么以太网一帧的大小被限制为1500？为什么padding最多是46？/帧最多是64

​​

【区分帧和垃圾数据】当一个收发器检测到冲突时，它会截断当前的帧，这意味着**冲突帧中已经送出的位将会出现在电缆上**。为了更加容易地区分==有效帧和垃圾数据==， 以太网要求有效帧必须至少64字节长，从目标地址算起直到校验和，包括这两个字段本身在内。如果帧的数据部分少于46个字节，则使用填充（Pad）字段来填充该帧，使其达到最小长度要求。

【防止冲突】这是一种机制，如果发生冲突则会产生48位长（？）的冲突噪声，所有帧必须至少需要2t时间才能完成发送,这样当突发噪声回到发送方时传送过程仍在进行。\
“对于一个最大长度为2500米、具有4个中继器的10 Mbps LAN(符合802.3规范),在最差情况下,往返一次的时间大约是50微秒(其中包括了通过4个中继器所需要的时间)。因此,允许的最小帧长必须至少需要这样长的时间来传输。以10 Mbps 的速率,发送一位需要100纳秒,所以500位是保证可以工作的最小帧长。考虑到加上安全余量,该值被增加到512位,或者64字节。“\
​​

#### 二进制指数回退算法

​​

​​

#### IEEE 802.2：逻辑链路控制协议

运行在以太网上，想上层网络层提供接口

​​

•LLC 3种服务类型:不可靠数据包服务,有确认的数据包服务,面向连接的可靠服务

RTT（Round-Trip Time，来回传输时间）

### 以太网性能分析

‍

​​

### 其他以太网

看一下就好

​​

### 其他局域网

placeholder，我猜肯定不考

### 数据链路层交换

​​

在这部分：\
两个局域网不同，网桥还要对不同的帧格式 进行重新封装\
如果两个局域网数据传输速率不同，则网桥还要进行 Buffering

理想的网桥是透明网桥（transparent bridges ），它可以将多个 LAN 连接起来，LAN 内的硬件和软件不需要做任何的变化。

但是如果内部出现环，怎么防治无限转发->每个网桥维护扩展树

### 各种连接网络的设备

​​

• 路由器：放置在“经典”网络中，并将数据包转发到其他路由器。\
• 传输网关：在传输层连接两个网络：从 TCP 连接到 ATM 传输连接。\
• 应用网关：连接两种不同的应用协议，例如将SMS 消息发送到Web 服务器，或将X.400 邮件系统连接到基于Internet 的邮件系统。

### VLAN

虚拟局域网（VLAN）可以将物理上不在同一 LAN 上的设备，在逻辑上归为同一组 LAN。**一个 VLAN 对应一个广播域**。有了 VLAN，可使用二层交换机实现广播域的分割。

事实上，更多的局域网桥接在一起才更好，更安全

​​

如果 VLAN 是跨交换机的，可以用 IEEE 802.1Q 协议，方法如下：在跨交换机之前，交换机会给帧打上一个 VLAN ID 标记，当到达另一个交换机后，交换机拆除 VLAN ID，并转发给对应端口。

​​