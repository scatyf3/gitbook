# 概要设计

## 概要设计

​![image](<.gitbook/assets/image 20231224171843 29eqhs9.png>)​

### 软件体系结构的内容

​​![image](<.gitbook/assets/image 20231224200825 74eqhqz.png>)​​

### 软件结构的设计过程：分层决策

• 复杂的设计决策过程\
•整体体系结构风格\
•编程语言和开发框架\
• 数据库及缓存\
•组件划分及组件间通信方式等\
• 每个决策都可能存在多种候选方案，各有利弊\
• 各种决策间还存在各种依赖关系\
•表现分层的决策过程

​![image](<.gitbook/assets/image 20231224200214 wvhbfa7.png>)​

### 软件体系结构的描述

​​![image](<.gitbook/assets/image 20231224200840 77gypfq.png>)​​

#### 文字描述

​​![image](<.gitbook/assets/image 20231224200848 5h17i68.png>)​​

​​![image](<.gitbook/assets/image 20231224200859 e4ssvay.png>)​​

​​![image](<.gitbook/assets/image 20231224200906 26q1mh4.png>)​​

#### 用例视图例子

​![image](<.gitbook/assets/image 20231224200931 ytmz9s7.png>)​

#### 逻辑视图

​![image](<.gitbook/assets/image 20231224200938 888juhl.png>)​

​![image](<.gitbook/assets/image 20231224200947 vzzqs2o.png>)​

#### 实现视图

​![image](<.gitbook/assets/image 20231224201041 x15fhpl.png>)​

#### 运行视图

​![image](<.gitbook/assets/image 20231224201051 hgru3n3.png>)​

​![image](<.gitbook/assets/image 20231224201058 2gm6lx7.png>)​

考察方式：给你一个视图，让你判断是什么

### 软件体系结构风格

* 层次化
* 知识库
* 管道-过滤器
* 微服务​​

### 层次化风格

​![image](<.gitbook/assets/image 20231224201946 h0fvklg.png>)​

 每个层次有明确的功能和职责划分,上层依赖于下层,而下层则不能依赖于上层\
 通过明确定义的层间接口隔离不同层次内部实现方式的影响\
 体现分解和抽象的设计原则\
• 通过层次划分将功能实现分解到不同层次上\
• 通过层间接口对每个层次的内部实现进行抽象

​![image](<.gitbook/assets/image 20231224201857 ncabxmz.png>)​

### 知识库风格

​![image](<.gitbook/assets/image 20231224202116 yf8lhza.png>)​

* 以数据为中心
* 以数据形式存在的知识在知识库中被集中保存和管理，围绕知识库的一组软件组件通过共享知识库间接进行交互
* 又称黑板风格，用于解决求解过程未知的人工智能问题

​![image](<.gitbook/assets/image 20231224202137 sqvfwit.png>)​

### 管道-过滤器风格

​![image](<.gitbook/assets/image 20231224202155 q388kgz.png>)​

一种数据流体系结构，即其中的组件是按照数据流进行组织\
“过滤器”表示实现数据加工和处理的软件组件，而“管道”则表示这些组件之间的连接关系并掌握数据流的走向\
组件间关系主要体现在输入输出数据流上，因此耦合相对较松

​![image](<.gitbook/assets/image 20231224202247 ghi3q9p.png>)​

### 微服务风格 todo

容器和容器化部署

## 分布式体系结构

### 设计原则-CAP定理

CAP定理(CAP Theorem)\
• 在2000年的分布式计算原理会议上,加州大学伯克利分校的计算机科学家Eric Brewer所做的题为《Towards Robust Distributed Systems》的主题演讲中的一个猜想,又被称作Brewer定理(Brewer’s Theorem)\
• 2002年,麻省理工学院的Nancy Lynch和Seth Gilbert证明了这一猜想(Lynch et al.,2002),使之成为一个定理

​![image](<.gitbook/assets/image 20231224203325 33glft7.png>)​

感觉这个真的要背诵了

### 设计原则-BASE定理

主要思想:牺牲数据的强一致性,换取高可用性,并在最后能实现最终一致性\
• 基本可用:不追求绝对的系统可用性,而是允许在服务质量有所下降(服务降级)的情况下确保基本的可用性\
• 软状态:又称弱状态,允许系统中不同节点上的数据处于一种不一致的中间状态,即不同节点上的数据更新可以存在一定的时间差\
• 最终一致性:系统中不同节点上的数据副本经过软状态的过渡后,最终都能够达到一致的状态

### 进程通信的模式

​![image](<.gitbook/assets/image 20231224203726 90wwzjl.png>)​

​​

• 组件间交互方式（从同步/异步模式、一对一/一对多的维度划分）\
• 一对一同步模式：客户端发出请求后阻塞等待，服务端会有一个唯一的实例处理该请求并实时响应\
• —对一异步模式：客户端发出请求后无需阻塞、继续执行，服务端会有一个唯一的实例处理请求并进行响应或不进行响应（客户端对服务端的单向通知）\
• 一对多的异步模式：客户端以消息的方式发出请求后继续执行，多个服务实例通过消息订阅收到通知，或者产生多个回调消息作异步响应

### 进程通信的实现方式

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

#### 远程调用

是一对一同步通信

• 采用某种开发框架,将业务逻辑实现与进程间通信相解耦\
• 请求方(即客户端)引入一个调用代理,其实现代码可以以本地调用的方式实现所需要的远程调用【桩】\
• 服务方(即服务端)引入一个包装器,将其业务逻辑实现包装成可以跨进程访问的服务【骨架】\
• 二者被称为**桩(stub)和骨架(skeleton)** ,一般由远程调用框架提供, 开发者无需了解其中的进程间通信实现方式

​​![](broken-reference)

例子\
RESTful API-通过http技术\
RPC-通过二进制消息-gRPC

#### 基于消息的通信

是一种异步通信模式

• 点对点\
• 发送方和接收方进行一对一通信,消息仅有一个接收方(即消费者),消息被读取处理后从队列移除

• 发布-订阅\
• 消息的发送方和接收方进行一对多的通信,发送方发布的每条消息都会标注相关的话题(Topic),接收方按照话题进行订阅并接收消息点对点模式

### 负载均衡

负载均衡:将大量请求负载以一种合理、均衡的方式分配到不同的服务器上

• 负载均衡器以软件的方式实现,并通过各种策略和算法实现负载均衡调度\
• 服务端负载均衡(主流)\
• 客户端负载均衡

服务端负载均衡-反向代理：处于外网的客户端发出请求后,服务端的负载均衡器选择一个内部的服务实例来处理此请求

客户端负载均衡：客户端发送请求时直接决定要访问的服务实例

常用的策略：

• 随机选择:随机从可用服务实例列表中选择一个进行请求\
•轮询（加权轮询） • 将请求轮流分配给各服务实例，可以根据处理能力为服务实例加上权重\
•最小负载（加权最小负载） •根据各服务实例当前的请求负载（例如等待队列的长度）分配请求，可以根据处理能力为服务实例加上权重\
• 最快响应速度•根据各服务实例的请求响应速度分配请求，但要排除服务实例在失效后快速返回的情况（即很快响应但实际上是失败）

### 分布式储存

目的：增加服务器和服务实例后,很多服务实例都需要访问持久化的数据存储(数据库或文件),从而使数据存储和访问成为瓶颈

部署在由多台服务器组成的集群上,一般采用主从(Master/Slave)结构进行组织\
• 一个主节点管理整个文件系统的命名空间以及客户端对文件的访问操作\
• 多个从节点管理所存储的数据(因此又称数据节点)\
• 主节点管理数据节点并向它们转发文件存取访问请求,如果一个数据节点无法正常工作则会被暂时移除\
• 每个数据块都会在多个数据节点上进行存储以实现冗余备份,主节点根据可用副本情况决定数据节点间的数据复制

• 对外提供统一的文件访问(例如创建、读取和更新文件),客户端无需了解内部结构\
• 典型代表是作为Hadoop 一部分的HDFS(Hadoop Distributed File System

​​![](<.gitbook/assets/image (6).png>)

分布式数据库-一般是非关系的数据库-Hbase,MongoDB

分布式缓存\
将经常被读取且较少变化的数据放到内存中,应用服务读取这些数据时无需访问数据库、只需要访问内存即可\
例子: Redis

### 可靠性保障

分布式软件系统面临外部不可预知的访问负载以及内部网络和服务器的各种不确定问题,如何确保其可靠运行是一个难题

策略：\
•限流：对于超过流量限制的请求拒绝访问，或者转入一个队列进行等待\
•降级：通过适当降低服务质量和用户体验来确保核心服务的可用性\
• 确保重要性较高的服务能够优先获得所需要的资源\
•常用的降级策略：停用优先级较低的服务、降低服务质量、降低信息或服务的时效性\
• 熔断：一种过载保护机制（类似于电路跳闸）\
•上游服务断开对下游服务的调用，避免局部的失败和延迟随着一层层的服务依赖扩散、蔓延和逐级放大•\
熔断过程：熔断条件监测、切断联系（快速失败或者服务降级）、恢复时机探测、恢复正常