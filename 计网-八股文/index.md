# 计网 八股文


# 计算机网络架构层次

## 你知道OSI七层参考模型吗

### 一. 物理层

负责物理传输媒介的传输，例如电缆、光纤或⽆线信号。主要作⽤是传输⽐特流（就是由 1、0 转化为电流强弱来进⾏传输，到达⽬的地后再转化为 1、0，也就是我们常说的数模转换与模数转换）。这⼀层的**数据叫做⽐特**

### 二. 数据链路层

建⽴逻辑连接、进⾏硬件地址寻址、**差错校验**等功能。定义了如何让格式化数据以帧为单位进⾏传输，以及如何控制对物理介质的访问。将⽐特组合成字节进⽽组合成帧，⽤ **MAC 地址**访问介质，**传输单位是桢**

### 三. 网络层

负责**数据的路由和转发**，选择最佳路径将数据从源主机传输到⽬标主机。它使⽤**IP地址**来标识不同主机和⽹络，并进⾏逻辑地址寻址。**传输单位是数据报**。常⻅的协议有**ICMP、ARP、IP**

### 四. 传输层

提供**端到端**的数据传输服务。它使⽤**TCP**（传输控制协议）和**UDP**（⽤户数据报协议）来管理数据传输。

### 五. 会话层

建⽴、管理和终⽌应⽤程序之间的会话连接。它处理会话建⽴、维护和终⽌，以及处理会话过程中的异常情况

### 六. 表示层

负责**数据的格式转换、加密和解密**，确保数据在不同系统之间的正确解释和呈现，也就是把计算机能够识别的东⻄转换成⼈能够能识别的东⻄（如图⽚、声⾳等）

### 七. 应用层

⽹络服务与最终⽤户的⼀个接⼝。这⼀层为⽤户的应⽤程序（例如电⼦邮件、⽂件传输和终端仿真）提供⽹络服务。常⻅的协议有：**FTP、SMTP、HTTP、DNS**


## TCP/IP四层网络模型？

TCP/IP模型是⼀种⽤于组织和描述计算机⽹络通信的标准模型，它是互联⽹最常⽤的协议栈。TCP/IP模型由两个主要协议组成：TCP（Transmission Control Protocol）和IP（Internet Protocol）

### 应用层

该层与OSI模型的**应⽤层和表示层**以及会话层类似，提供直接与⽤户应⽤程序交互的接⼝。它为⽹络上的各种应⽤程序提供服务，如电⼦邮件（SMTP）、⽹⻚浏览（HTTP）、⽂件传输（FTP）等

### 传输层

传输层（Transport Layer）：该层**对应OSI模型的传输层** 。它负责端到端的数据传输，提供可靠的、⽆连接的数据传输服务。主要的传输层协议有**TCP**（Transmission Control Protocol）和**UDP**（User Datagram Protocol）。TCP提供可靠的数据传输，确保数据的正确性和完整性；⽽UDP则是⽆连接的，适⽤于不要求可靠性的传输，如实时⾳频和视频流

### 网际层

该层对应OSI模型的 **⽹络层**。主要协议是IP（Internet Protocol），它负责数据包的路由和转发，选择最佳路径将数据从源主机传输到⽬标主机。IP协议使⽤IP地址来标识主机和⽹络，并进⾏逻辑地址寻址

### 网络接口层

⽹络接⼝层（Link Layer）：该层对应OSI模型的**数据链路层和物理层**。它负责物理传输媒介的传输，例如以太⽹、Wi-Fi等，并提供错误检测和纠正的功能。此外，⽹络接⼝层还包含硬件地址（MAC地址）的管理




## HTTPS和HTTP的区别?

- **HTTP：** 以**明⽂**的⽅式在⽹络中传输数据，HTTPS 解决了HTTP 不安全的缺陷，在 TCP 和 HTTP ⽹络层之间加⼊了 SSL/TLS 安全协议，使得报⽂能够**加密传输。**

- HTTPS 在 TCP 三次握⼿之后，还需进⾏ **SSL/TLS 的握⼿过程**，才可进⼊加密报⽂传输。HTTPS采⽤对称加密和⾮对称加密结合的【混合加密】⽅式，保证信息的机密性，解决了窃听的⻛险。

- HTTP 的端⼝号是 80，HTTPS 的端⼝号是 443。
  
- HTTPS 协议需要向 CA（证书权威机构） 申请数字证书，来保证服务器的身份是可信的。


## 什么对称加密？

对称加密也称为私钥加密，使⽤相同的密钥来进⾏加密和解密。

- 在加密过程中，明⽂数据通过应⽤**特定的算法和密钥**进⾏加密，⽣成密⽂数据。解密过程则是将密⽂数据应⽤同样的算法和密钥进⾏解密，恢复为明⽂数据。

- 由于加密和解密都使⽤相同的密钥，因此对称加密算法的速度通常较快，但密钥的安全性很重要。如果密钥泄漏，攻击者可以轻易地解密数据。


## 什么是非对称加密？

- ⾮对称加密也称为公钥加密，使⽤⼀对不同但相关的密钥：公钥和私钥。

- 公钥⽤于**加密数据**，私钥⽤于**解密数据**。如果使⽤公钥加密数据，只有拥有相应私钥的⼈才能解密数据；如果使⽤私钥加密数据，可以使⽤相应公钥解密。除了加密和解密，⾮对称加密还⽤于【数字签名】，可以验证消息的来源和完整性。


## 为什么需要 TIME_WAIT 状态？

主动发起关闭连接的⼀⽅，才会有 TIME-WAIT 状态。

需要 TIME-`WAIT` 状态，主要是两个原因：
1. 防⽌历史连接中的数据，被后⾯相同四元组的连接错误的接收；

如果⽹络出现拥塞或延迟，数据包可能会在⽹络中滞留⼀段时间，甚⾄超过了原始连接关闭的时间。如果没有 `TIME_WAIT` 状态，客户端直接进⼊到 `CLOSE` 状态，这些滞留的数据包可能会被传递给新连接，导致新连接的数据被旧连接的数据⼲扰。

经过 2MSL 这个时间，⾜以让两个⽅向上的数据包都被丢弃，使得原来连接的数据包在⽹络中都⾃然消失，再出现的数据包⼀定都是新建⽴连接所产⽣的。

2. 保证「被动关闭连接」的⼀⽅能被正确的关闭，即保证最后的 ACK 能让被动关闭⽅接收，从⽽帮助其正常关闭 

如果最后的⼀次ACK报⽂丢失（第四次挥⼿），客户端没有 `TIME_WAIT` 状态，直接进⼊ClOSE，服务端⼀直在等待ACK状态，⼀直没有等到，就会重发FIN报⽂，⽽客户端已经进⼊到关闭状态，在收到服务端重传的 FIN 报⽂后，就会回 RST 报⽂,服务端收到这个 RST 并将其解释为⼀个错误, 为了防⽌这种情况出现，客户端必须等待⾜够⻓的时间，确保服务端能够收到 ACK，如果服务端没有收到 ACK，那么就会触发 TCP 重传机制，服务端会重新发送⼀个FIN，这样⼀去⼀来刚好两个 MSL 的时间。

## 为什么 TIME_WAIT 等待的时间是 2MSL？

1. MSL是 Maximum Segment Lifetime ，报⽂最⼤⽣存时间，它是任何报⽂在⽹络上存在的最⻓时间，超过这个时间报⽂将被丢弃。
   
2. 等待MSL两倍：⽹络中可能存在发送⽅的数据包，当这些发送⽅的数据包被接收⽅处理后⼜会向对⽅发送响应，所以⼀来⼀回需要等待 2 倍的时间。
   
3. 1 个 MSL 确保四次挥⼿中主动关闭⽅最后的 ACK 报⽂最终能达到对端；1 个 MSL 确保对端没有收到 ACK 重传的 FIN 报⽂可以到达。
   
4. 2MSL 的时间是从客户端接收到 FIN 后发送 ACK 开始计时的。如果在 TIME-`WAIT` 时间内，因为客户端的 ACK没有传输到服务端，客户端⼜接收到了服务端重发的 FIN 报⽂，那么 2MSL 时间将重新计时

## TIME_WAIT 过多有什么危害？

过多的 TIME-`WAIT` 状态主要的危害有两种：一是会占用内存资源；二是占用端口，因为一个TCP连接至少消耗一个本地端口；

- 如果客户端的 `TIME_WAIT` 状态过多，占满了所有端⼝资源，则会导致⽆法创建新连接。

- 如果服务器端的 `TIME_WAIT` 状态过多，会占用大量的内存资源，没法相应新的连接。


## TCP与UDP的区别？
- **是否面向连接 ：** UDP 在传送数据之前不需要先建立连接。而 TCP 提供面向连接的服务，在传送数据之前必须先建立连接，数据传送结束后要释放连接。

- **是否是可靠传输：** 远地主机在收到 UDP 报文后，不需要给出任何确认，并且不保证数据不丢失，不保证是否顺序到达。TCP 提供可靠的传输服务，TCP 在传递数据之前，会有三次握手来建立连接，而且在数据传递时，有确认、窗口、重传、拥塞控制机制。通过 TCP 连接传输的数据，无差错、不丢失、不重复、并且按序到达。

- **是否有状态 ：** 这个和上面的“是否可靠传输”相对应。TCP 传输是有状态的，这个有状态说的是 TCP 会去记录自己发送消息的状态比如消息是否发送了、是否被接收了等等。为此 ，TCP 需要维持复杂的连接状态表。而 UDP 是无状态服务，简单来说就是不管发出去之后的事情了。
  
- **传输效率 ：** 由于使用 TCP 进行传输的时候多了连接、确认、重传等机制，所以 TCP 的传输效率要比 UDP 低很多。
  
- **传输形式 ：** TCP 是面向字节流的，UDP 是面向报文的。

- **首部开销 ：** TCP 首部开销（20 ～ 60 字节）比 UDP 首部开销（8 字节）要大。

- **是否提供广播或多播服务 ：** TCP 只支持点对点通信，UDP 支持一对一、一对多、多对一、多对多；

- UDP 一般用于**即时通信**，比如： 语音、 视频 、直播等等。这些场景对传输数据的准确性要求不是特别高，比如你看视频即使少个一两帧，实际给人的感觉区别也不大。
  
- TCP 用于对**传输准确性要求特别高**的场景，比如文件传输、发送和接收邮件、远程登录等等。

## TCP中的元组？
**四元组是:** 源IP地址、目的IP地址、源端口、目的端口

**五元组是:** 源IP地址、目的IP地址、协议号、源端口、目的端口

**七元组是:** 源IP地址、目的IP地址、协议号、源端口、目的端口，服务类型以及接口索引

## TCP是怎么保持长链接的？
TCP长连接保持：`KeepAlive` 。TCP协议的实现里有一个 `KeepAlive` 机制，自动检测能否和对方连通并保持连接。

保活机制默认是关闭的，TCP连接的**任何一方**都可打开此功能。有三个主要配置参数用来控制保活功能。

向对端发送一个保活探测报文。

- 若对端正常存活，且连接有效，对端必然能收到探测报文并进行响应。此时，发送端收到响应报文则证明TCP连接正常，重置保活时间计数器即可。

- 若由于网络原因或其他原因导致，发送端无法正常收到保活探测报文的响应。那么在一定探测时间间隔（`tcp_keepalive_intvl`） 后，将继续发送保活探测报文。直到收到对端的响应，或者达到配置的探测循环次数上限（tcp_keepalive_probes） 都没有收到对端响应，这时对端会被认为不可达，TCP连接随存在但已失效，需要将连接做中断处理。

**TCP的探活(心跳)是多长时间？**

- 保活时间（`tcp_keepalive_time`）默认：7200秒

- 保活时间间隔（`tcp_keepalive_intvl`）默认：75秒

- 探测循环次数（`tcp_keepalive_probes`）默认：9次

也就是默认情况下一条TCP连接在2小时（7200秒）都没有报文交换后，会开始进行保活探测，若再经过9*75秒=11分钟15秒的循环探测都未收到探测响应，即共计：2小时11分钟15秒后会自动断开TCP连接。

**和HTTP的 keep-alive 的区别？**

http协议是一个运行在TCP协议之上的无状态的应用层协议。它的特点是：客户端的每一次请求都要和服务端创建TCP连接，服务器响应后，断开TCP连接。下次客户端再有请求，则重新建立连接。

在早期的http1.0中，默认就是上述介绍的这种“请求-应答”模式。这种方式频繁的创建连接和销毁连接无疑是有一定性能损耗的。所以引入了keep-alive机制。http1.0默认是关闭的，通过http请求头设置“connection: keep-alive”进行开启；http1.1中默认开启，通过http请求头设置“connection: close”关闭。

keep-alive机制：若开启后，在一次http请求中，服务器进行响应后，不再直接断开TCP连接，而是将TCP连接维持一段时间。在这段时间内，如果同一客户端再次向服务端发起http请求，便可以复用此TCP连接，向服务端发起请求，并重置timeout时间计数器，在接下来一段时间内还可以继续复用。这样无疑省略了反复创建和销毁TCP连接的损耗。

## HTTP1.0 与 HTTP1.1的区别？

- 使用长连接的方式改善了 `HTTP/1.0` 短连接造成的性能开销。HTTP 1.1起，默认使用长连接 ,默认开启 Connection： keep-alive。 HTTP/1.1的持续连接有非流水线方式和流水线方式 。流水线方式是客户在收到HTTP的响应报文之前就能接着发送新的请求报文。与之相对应的非流水线方式是客户在收到前一个响应后才能发送下一个请求。

- 在HTTP1.1中新增了24个错误状态响应码，如409（Conflict）表示请求的资源与资源的当前状态发生冲突；410（Gone）表示服务器上的某个资源被永久性的删除。

- 带宽优化及网络连接的使用：HTTP1.0中，存在一些浪费带宽的现象，例如客户端只是需要某个对象的一部分，而服务器却将整个对象送过来了，并且不支持断点续传功能，HTTP1.1则在请求头引入了range头域，它允许只请求资源的某个部分，即返回码是206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接。

**HTTP/1.1 自身的性能瓶颈：**

1. 请求/响应头部（Header）未经压缩就发送，首部信息越多延迟越大。只能压缩Body的部分
2. 发送冗长的首部。每次互相发送相同的首部造成的浪费较多
3. 服务器是按请求的顺序响应的，如果服务器响应慢，会招致客户端一直请求不到数据，也就是队头阻塞
4. 没有请求优先级控制
5. 请求只能从客户端开始，服务器只能被动响应


## 全连接队列和半连接队列有了解吗？

<center>

![连接队列与三次握手](/img/全连接半连接.png)

</center>

在 TCP 三次握手的时候，Linux 内核会维护两个队列，分别是：

- 半连接队列，也称 SYN 队列；哈希表
- 全连接队列，也称 `accept` 队列；链表

服务端收到客户端发起的 SYN 请求后，内核会把该连接存储到半连接队列，并向客户端响应 SYN+ACK，接着客户端会返回 ACK，服务端收到第三次握手的 ACK 后，内核会把连接从半连接队列移除，然后创建新的完全的连接，并将其添加到 `accept` 队列，等待进程调用 accept 函数时把连接取出来。

不管是半连接队列还是全连接队列，都有最大长度限制，超过限制时，内核会直接丢弃，或返回 RST 包。默认是丢弃。

使用命名 `cat proc/sys/net/ipv4/tcp_abort_on_overflow `查看策略的配置：

- 0 是表示如果全连接队列满了，那么 server 扔掉 client 发过来的 ack ；
- 1 是表示如果全连接队列满了，server 发送一个 reset 包给 client，表示废掉这个握手过程和这个连接

**那么使用什么命令查看全连接队列的大小呢？**

可以使用 `ss`命令进行查看。使用 `ss -help`可查看 `ss`指令的所有功能与用法。

如 `ss -lnt` 就是查看正在监听的 TCP `socket` ：
```
wxs-605@hecs-135751:~$ ss -lnt
State                  Recv-Q                 Send-Q                                 Local Address:Port                                 Peer Address:Port                Process                 
LISTEN                 0                      1024                                   127.0.0.53%lo:53                                        0.0.0.0:*                                           
```

Recv-Q就是当前全连接队列的大小，Send-Q就是全连接队列的最大长度。

同时可使用命令 `netstat -s | grep overflowed` 来查看全连接队列的溢出次数。

**那么使用什么命令查看半连接队列的大小呢？**

`netstat -natp | grep SYN_RECV | wc -l`

同时可使用命令 `netstat -s | grep "SYNs to LISTEN" `来查看半连接队列的溢出长度。

