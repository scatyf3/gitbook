# 计算机网络 传输层笔记

## 计算机网络 传输层笔记

## 传输层综述

 The transport layer protocols provides logical communication between two application processes (not hosts):\
◼ Provides reliable connection-oriented services ◼ Provides unreliable connectionless services ◼ Provides parameters for specifying QoS

## socket接口

​![image](<.gitbook/assets/image 20231229031827 szhnxmv.png>)​

## 传输层协议的要素

 Addressing\
 Connection Establishment\
 Connection Release\
 Flow Control and Buffering\
 Multiplexing\
 Crash Recovery

传输单元：transport protocol data unites (TPDUs)

建立和释放连接：对称和不对称

面对的问题和数据链路层协议有点相似，但是在面对问题的解决方案上有大大不同\
​![image](<.gitbook/assets/image 20231229032102 qccmxvz.png>)​

### 编址

a transport service access point is an IP address with a port number

​​

​![image](<.gitbook/assets/image 20231229033609 m2ybr62.png>)​

确定地址：固定地址的dns和其他不固定地址的服务?【是我理解的下面的意思吗？】

那么怎么知道应用与那个 TSAP 关联呢？有两种方法，一种是某些应用约定俗成地绑定在某些端口上（如邮件服务器在 25）；另一种是，先与端口映射器连接（它总是在某个知名端口上），然后通过端口映射器获取应用的端口，再释放连接，重新连接到那个端口。\
一台机器上有多个服务器进程，如果让每个服务都监听一个 TSAP 地址，则很浪费资源。一般是只有一个特殊的 **进程服务器** 负责监听一组端口，当有请求时，再将服务器与端口连接，这种方案叫 **初始连接协议**

### 建立连接

Basic idea: 为建立连接, 向对方发送一个 connection request. 然后对方接受连接请求并返回一个ack.

问题：ack消失，导致请求建立连接两次，所以我们需要somehow防止重复\
​![image](<.gitbook/assets/image 20231229033835 w6mjodr.png>)​

#### 抗重

> ### 连接建立
>
> 建立连接的基本过程包括发送 CONNECTION REQUEST 和等待 CONNECTION ACCEPTED 应答，此外，还要考虑网络可能丢失、延迟、重复数据包。想象用户与银行建立了连接，并发起了转账，结果数据包延迟了，这样数据会重传，但不幸的是，延迟的数据包又冒了出来，导致有两笔转账。为了避免这种错误，有几种方案：
>
> 1. ❌每个连接有一个唯一地址，当连接释放时，地址被丢弃且不重复使用。这种坏处是，会使得首次建立连接变得超级困难
> 2. ❌每个连接有唯一标识符。坏处是，源机器和目标机器上的标识符要保持一致，并且要无限期维护历史信息，万一哪一天机器崩溃导致信息丢失，那就完 jb 蛋。
> 3.  ⭕不允许数据包在网络中无限存在，而是有一定生存期，生存期可以通过下面几种技术设定：
>
>     * 限制网络设计（避免数据包进入循环，并限制延迟）
>     * 使用跳计数器
>     * 为数据包打上时间戳
>
>     Internet 的生存期是 120 秒。由于要保证数据包和确认都死亡，所以实际要等待生存期的倍数时间 T 后，才可认为该数据包的所有痕迹都没了。在这个基础上，发送方给每个段一个序号，并且该序号在 T 内不被重用。这样收方就能丢弃延迟或重复的包。
>
> 在第三种方案的基础上，Tomlinson 提出一种编号的方法：每个主机有一个独立的日时钟（time-of-day clock），该时钟用二进制计时，时钟的低 k 位用于初始化序号。序号要有足够位数。假设时钟速率为 C，序号空间为 S，那么，S/C>T。另外，当主机停机时，时钟不会停下。
>
> 下图（a）中，从 0 开始的粗线表示时钟，为了避免前面的序号延时 T 后与初始序号重复，在一定时间内不能使用禁止区域中的序号。所以，为了避免发送速度过快导致序号落在禁止区域，段发送速度不能太快，最大速率为时钟速率。同时不能太慢，太慢会像图（b）那样进到后面的禁止区域。
>
> ​![传输层 Tomlinson](<.gitbook/assets/gXiudy5427JQWUo 20231228212654 ol30mcm.jpg>)​
>
> 但这种方法依然不能用于建立连接。因为收方无法根据收到的初始序号判断出是不是重复连接。所以我们引入了​**三次握手**​，假设 A 向 B 发起连接：
>
> 1. A 发送序号 x 的 CONNECTION REQUEST，即 seq=x
> 2. B 回应一个 ACK，并宣告自己的序号 y，即 seq=y,ack=x+1
> 3. A 对 B 选择的 y 进行确认，即 seq=x+1,ack=y+1
>
> ​![](<.gitbook/assets/figure\_2.2 20231228212654 rkn5s99.png>)​
>
> 这样一来，如果收到重复连接请求，那么 B 会发送应答，A 收到应答后发现这是个重复连接请求，就会拒绝 B，这样 B 也就知道这是个重复连接。（见下图）
>
> ​![三次握手重复请求](<.gitbook/assets/35hPcHlNztSsLWu 20231228212654 1ukpnph.jpg>)​
>
> 更复杂的情况下，连接请求和确认都重复了（如下图），B 会在第三次握手的时候发现连接请求重复而丢弃改连接，而同时，B 在收到重复确认时，也能根据 ACK 的不同发现这个重复了。
>
> ​![三次握手重复请求2](<.gitbook/assets/SVRp75OXFia3rZh 20231228212654 g5vhhqm.jpg>)​

【这个方案就是第三种，只不过本地不及时而是记序列号，但是对于序列号的发送不能太快也不能太慢】

#### 无错

不可能双方序列号相同，所以我们采用三路握手

​![image](<.gitbook/assets/image 20231229034914 s8akrw2.png>)​

### 释放

非对称释放会导致丢失数据；

​![image](<.gitbook/assets/image 20231229035009 wr650zv.png>)​

​​

对称释放比较难...一个务实的解决方案是使用超时机制，但是仍然存在问题

​![image](<.gitbook/assets/image 20231229035021 bazjetn.png>)​

​![image](<.gitbook/assets/image 20231229035318 md07vr0.png>)​

【这个三次握手+超时好像是现实世界的方案】

这个问题无法解决，所以干脆也用三次握手：

1. A 发送 DR，并启动计时器
2. B 发送 DR 并启动计时器
3. A 收到 B 的 DR，释放连接，回送 ACK
4. B 收到 ACK，释放连接

### Flow control and buffer

#### 缓存window

发送接收双方协商buffer容量->考虑网络流量，根据它计算buffer（谁计算？）

那么有了窗口，就可以指定窗口大小，窗口大小就是指**无需等待确认应答，而可以继续发送数据的最大值**。

​​

* ​`SND.WND`​：表示发送窗口的大小（大小是由接收方指定的）；
* ​`SND.UNA`​（_Send Unacknoleged_）：是一个绝对指针，它指向的是已发送但未收到确认的第一个字节的序列号，也就是 #2 的第一个字节。
* ​`SND.NXT`​：也是一个绝对指针，它指向未发送但可发送范围的第一个字节的序列号，也就是 #3 的第一个字节。

**可用窗口大小 = SND.WND -（SND.NXT - SND.UNA）**

**如果窗口大小为 0 时，就会阻止发送方给接收方传递数据，直到窗口变为非 0 为止，这就是窗口关闭。**

> 窗口关闭潜在的危险

接收方向发送方通告窗口大小时，是通过 `ACK`​ 报文来通告的。

那么，当发生窗口关闭时，接收方处理完数据后，会向发送方通告一个窗口非 0 的 ACK 报文，如果这个通告窗口的 ACK 报文在网络中丢失了，那麻烦就大了。

#### 流量控制

​​

在协商中怎么避免死锁->启动计时器

​​

* 如果接收窗口仍然为 0，那么收到这个报文的一方就会重新启动持续计时器；
* 如果接收窗口不是 0，那么死锁的局面就可以被打破了。

窗口探测的次数一般为 3 次，每次大约 30-60 秒（不同的实现可能会不一样）。如果 3 次过后接收窗口还是 0 的话，有的 TCP 实现就会发 `RST`​ 报文来中断连接。

若网络每秒能传输c TPDUs for a stream, 花费r seconds to transmit-> let the sender estimate c and r and adjust its own number of buffers

### 复用

有两种复用

* 向上复用：一个虚电路支持多个传输层链接
* 向下复用：多个虚电路支持一个传输层链接

​![image](<.gitbook/assets/image 20231229041443 2aj0pwh.png>)​

### 崩溃恢复

 Problem: 假定服务器收到一个 TPDU，然后按要求执行一 个**operation and returning an acknowledgment**(结果). ◼ 若服务器before, during, or after its response崩溃的话，发送主机 咋办？

​![image](<.gitbook/assets/image 20231229042101 15cve32.png>)​

• S0: sender had no outstanding (unacknowledged) TPDUs\
• S1: sender had one outstanding TPDU(未决tpdu)

总体来说，从**第N层崩溃中恢复只能由第N+1层来完成**，并且==只能==​\*\*==在第N+1层留有足够状态信息==\*\*​==的情况下才能完成==。

对于**传输层崩溃恢复**，需要先了解在**崩溃前发送主机和接收主机的状态**，以及**发送主机对最后TPDU的处理**。崩溃恢复工作必须**由更高层次（应用层）来完成**

## 现实生活中的传输层

### UDP

​![image](<.gitbook/assets/image 20231229042243 e79azal.png>)​

长度上限：UDP packets cannot be larger than 64K

是简单，有端口的升级版IP协议

usage：Remote Procedure Call (RPC)

### RTP

对流媒体的针对性加强？

开始被设计为一个[多播](https://zh.wikipedia.org/wiki/%E5%A4%9A%E6%92%AD)协议，但后来被用在很多[单播](https://zh.wikipedia.org/wiki/%E5%96%AE%E6%92%AD)应用中。RTP协议常用于[流媒体](https://zh.wikipedia.org/wiki/%E6%B5%81%E5%AA%92%E4%BD%93)系统（配合RTSP协议），视频会议和[一键通](https://zh.wikipedia.org/w/index.php?title=%E4%B8%80%E9%94%AE%E9%80%9A\&action=edit\&redlink=1)（Push to Talk）系统（配合H.323或SIP），使它成为[IP电话](https://zh.wikipedia.org/wiki/IP%E7%94%B5%E8%AF%9D)产业的技术基础。

​![image](<.gitbook/assets/image 20231229042446 mq6xl6h.png>)​

曾加时间戳机制：\
◼ 属于同一子流的数据包可以接收一个时间戳，指示它们的距离相对于他们的前帧。\
◼ 这种方法允许系统减少抖动。\
◼ 此外，还可以使用时间戳来同步多个子流（例如，具有两个音频的视频频道）。

### TCP

​​

### 报文格式解析

​![image](<.gitbook/assets/image 20231229042755 fhzj8jz.png>)​

​![image](<.gitbook/assets/image 20231229042815 1jujip5.png>)​

各种控制参数的解释，懒得看了

maximum total length of 65535 bytes

【以下可以直接翻“互联网大厂面试八股”】

### TCP 拥塞控制

**在网络出现拥堵时，如果继续发送大量数据包，可能会导致数据包时延、丢失等，这时 TCP 就会重传数据，但是一重传就会导致网络的负担更重，于是会导致更大的延迟以及更多的丢包，这个情况就会进入恶性循环被不断地放大....**

​![image](<.gitbook/assets/image 20231229042913 yefvvf7.png>)​

主要看慢启动算法，就是**当发送方每收到一个 ACK，拥塞窗口 cwnd 的大小就会加 1，指数增长【看图】**

> #### [#](https://xiaolincoding.com/network/3\_tcp/tcp\_feature.html#%E6%85%A2%E5%90%AF%E5%8A%A8) 慢启动
>
> TCP 在刚建立连接完成后，首先是有个慢启动的过程，这个慢启动的意思就是一点一点的提高发送数据包的数量，如果一上来就发大量的数据，这不是给网络添堵吗？
>
> 慢启动的算法记住一个规则就行：**当发送方每收到一个 ACK，拥塞窗口 cwnd 的大小就会加 1。**
>
> 这里假定拥塞窗口 `cwnd`​ 和发送窗口 `swnd`​ 相等，下面举个栗子：
>
> * 连接建立完成后，一开始初始化 `cwnd = 1`​，表示可以传一个 `MSS`​ 大小的数据。
> * 当收到一个 ACK 确认应答后，cwnd 增加 1，于是一次能够发送 2 个
> * 当收到 2 个的 ACK 确认应答后， cwnd 增加 2，于是就可以比之前多发2 个，所以这一次能够发送 4 个
> * 当这 4 个的 ACK 确认到来的时候，每个确认 cwnd 增加 1， 4 个确认 cwnd 增加 4，于是就可以比之前多发 4 个，所以这一次能够发送 8 个。
>
> 慢启动算法的变化过程如下图：
>
> ​​
>
> 可以看出慢启动算法，发包的个数是​**指数性的增长**​。
>
> > 那慢启动涨到什么时候是个头呢？
>
> 有一个叫慢启动门限 `ssthresh`​ （slow start threshold）状态变量。
>
> * 当 `cwnd`​ < `ssthresh`​ 时，使用慢启动算法。
> * 当 `cwnd`​ >= `ssthresh`​ 时，就会使用「拥塞避免算法」。

到达临界值，会使用拥塞避免算法，**每当收到一个 ACK 时，cwnd 增加 1/cwnd**

> #### [#](https://xiaolincoding.com/network/3\_tcp/tcp\_feature.html#%E6%8B%A5%E5%A1%9E%E9%81%BF%E5%85%8D%E7%AE%97%E6%B3%95) 拥塞避免算法
>
> 前面说道，当拥塞窗口 `cwnd`​ 「超过」慢启动门限 `ssthresh`​ 就会进入拥塞避免算法。
>
> 一般来说 `ssthresh`​ 的大小是 `65535`​ 字节。
>
> 那么进入拥塞避免算法后，它的规则是：**每当收到一个 ACK 时，cwnd 增加 1/cwnd。**
>
> 接上前面的慢启动的栗子，现假定 `ssthresh`​ 为 `8`​：
>
> * 当 8 个 ACK 应答确认到来时，每个确认增加 1/8，8 个 ACK 确认 cwnd 一共增加 1，于是这一次能够发送 9 个 `MSS`​ 大小的数据，变成了**线性增长。**
>
> 拥塞避免算法的变化过程如下图：
>
> ​​
>
> 所以，我们可以发现，拥塞避免算法就是将原本慢启动算法的指数增长变成了线性增长，还是增长阶段，但是增长速度缓慢了一些。
>
> 就这么一直增长着后，网络就会慢慢进入了拥塞的状况了，于是就会出现丢包现象，这时就需要对丢失的数据包进行重传。
>
> 当触发了重传机制，也就进入了「拥塞发生算法」。

### TCP重传机制

拥塞发生，进行超时重传

迭代计算估计的RTT，使之接近真实$RTT=\alpha RTT + (1-\alpha) M $，rtt是估计值，m是发出-收到![image](<.gitbook/assets/image 20231229042926 ejhi502.png>)​

【tcp与数据链路层比，rtt方差**大**】

​​

‍

### 链接建立和释放

​![image](<.gitbook/assets/image 20231229042947 vjgbusd.png>)​

​![客户端主动关闭连接 —— TCP 四次挥手](https://cdn.xiaolincoding.com/mysql/other/format,png-20230309230614791.png)​

### TCP传输策略和滑动窗口

​​

### “无线”tcp

一般而言，丢包是由于拥塞，所以我们要让链接慢下来；但是在无线环境下，也有可能是不可靠，所以反而要加快传输

​​

 Solution 1: split TCP connections to distinguished wired/wireless IP:第一个连接上的超时，会导致发送方减速，第二个会加速。\
 Solution 2: let the base station do at least some retransmissions, but without informing the source. Effectively, the base station makes an attempt to improve the reliability of IP as perceived by TCP.
