# Socket 网络编程


今天学习了`Socket`网络编程，记录一下相关的知识点。

### 什么是 Socket ？

Socket 既套接字，就是对网络中不同主机上的应用进程之间进行双向通信的端点的抽象。一个套接字就是网络上进程通信的一端，提供了应用层进程利用网络协议交换数据的机制。套接字用于描述IP地址和端口，是一个通信链的句柄。有篇 [博客](https://syxdevcode.github.io/2021/09/13/Socket%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B/) 举了个例子，我觉得很形象：

以一个电话网为例：
1. 电话的通话双发相当于网络上相互通信的两个程序，电话号码就是 IP 地址；
2. 任何用户通话之前，首先要有一部电话机，相当于申请一个 Socket ；同时要知道对方的号码，相当于对方有一个固定的 Socket；
3. 向对方拨号呼叫，相当于发出连接请求。对方假如在场并空闲，拿起电话话筒，双方就可以正式通话，相当于连接成功；
4. 双方通话的过程，是一方向电话机发出信号和对方从电话机接收信号的过程，相当于向Socket发送数据和从Socket接收数据；
5. 通话结束后，一方挂起电话机相当于关闭Socket，撤销连接；

在C++中，Socket编程通常使用BSD socket API，这个API由Berkeley Unix开发，它提供了一套标准的接口，可用于在不同的平台上进行网络编程。使用socket API，你可以创建、绑定和监听套接字，同时还可以使用send()和recv()函数发送和接收数据。

Socket编程的基本步骤如下：

- 创建套接字：使用socket()函数创建套接字，它返回一个新的套接字描述符，以便后续使用。

- 绑定地址：使用bind()函数将套接字与本地IP地址和端口号绑定。

- 监听连接：使用listen()函数开始监听传入的连接请求。

- 发起连接：使用connect()函数向特定服务器发起连接请求。

- 接受连接：使用accept()函数接受传入的连接请求，创建一个新的套接字描述符并返回。

- 发送和接收数据：使用send()和recv()函数发送和接收数据。

- 关闭套接字：使用close()函数关闭套接字连接。

### socket() 函数
创建一个新的套接字，并返回一个描述符供后续操作使用

`int socket(int domain, int type, int protocol);`

- domain：表示协议域，它指定了所用的地址类型。常见的有 `AF_INET`（IPv4），`AF_INET6`（IPv6），`AF_UNIX`（本地通信），`AF_UNSPEC`（未指定）等;
- type：表示套接字类型，它指定了套接字的传输方式。常见的有 `SOCK_STREAM`（流式套接字，即TCP），`SOCK_DGRAM`（数据报套接字，即UDP），`SOCK_RAW`（原始套接字）等;
- protocol：表示协议类型，通常可以设为0。如果在给定的地址族和套接字类型下有多个协议可供选择，可以指定特定的协议;


###  bind() 函数
将套接字与特定的IP地址和端口号绑定

`int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);`

- sockfd：套接字描述符;
-  addr：指向存放地址信息的结构体指针，通常为 `struct sockaddr_in` ;
- addrlen：地址结构体的长度;


**注意点**
通常服务器在启动时都会绑定一个众所周知的地址（如 IP 地址 + 端口号），用于接收客户的服务请求，用户就可以通过向它发送请求来连接服务器；而客户端就不用指定，因为有系统自动分配的一个端口和自身的 IP 地址组合作为标识。这就是为什么通常服务器端在调用 listen（）之前会先调用 bind（）绑定地址，而客户端就不用，而是在调用 connect（）时由系统随机生成一个。


###  listen() 函数
开始监听传入的连接请求

`int listen(int sockfd, int backlog);`

- sockfd：套接字描述符
- backlog：指定在拒绝新连接之前，操作系统可以排队的最大连接个数。


### connect() 函数
客户端调用此函数来建立于TCP服务器的连接

`int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);`

- sockfd：客户端的套接字描述符;
-  addr：想要请求连接的服务器的 socket 地址，为结构体类型;
- addrlen：地址结构体的长度;


### accept() 函数
接受传入的连接请求，创建一个新的套接字描述符并返回

`int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);`

- sockfd：套接字描述符，指的是服务器的 socket 描述符;
- addr：用于存放客户端地址信息的结构体指针;
- addrlen：地址结构体的长度;


**注意**
accept（）函数的第一个参数为服务器用于监听请求的 socket 描述符，是服务器开始调用 socket（）函数生成的，称为监听 socket 描述符；而 accept（）函数返回的是已建立连接的 socket 描述符。一个服务器通常通常仅仅只创建一个**监听 socket 描述符** ，它在该服务器的生命周期内一直存在。内核为每个由服务器进程接受的客户连接创建了一个已连接 socket 描述符，当服务器完成了对某个客户的服务，相应的已连接socket 描述符就被关闭。


### close() 函数
关闭已经建立连接的 socket

`int close(int fd);`

- fd：套接字描述符；


### Socket 中实现TCP三次握手的过程

-----------

<div align="center">    
<img src="/img/socket-三次握手.png" alt="server" width="150%" height="150%">
</div>

--------------

#### 第一次握手时
- 客户端：调用 connect（）时，触发了连接请求，向服务器发送了 SYN_J 包，这时 connect（）函数进入阻塞状态，等待响应；
- 服务器端：监听到连接请求，即收到 SYN_J 包；

#### 第二次握手时
- 服务器端：调用 accept（）函数接收请求向客户端发送 SYN_K ，ACK_J + 1，这时 accept（）进入阻塞状态，等待客户端 ACK 回复；
- 客户端：收到服务器的 SYN_K ，ACK_J + 1之后，这时 connect（）返回，并对 SYN_K 进行确认；

#### 第三次握手：
- 客户端：向服务端发送 ACK_K + 1，进行确认；
- 服务器端：收到 ACK_K+1时，accept（）返回，至此三次握手完毕，连接建立。

### 验证 socket 的小例子
服务器端代码(server.cpp):

```cpp
#include <iostream>
#include <string>
#include <sys/socket.h>
#include <netinet/in.h>
#include <unistd.h>

int main() {
    // 创建套接字
    int server_fd = socket(AF_INET, SOCK_STREAM, 0); 

    // 绑定地址和端口
    struct sockaddr_in server_addr;
    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = INADDR_ANY;
    server_addr.sin_port = htons(8888);
    bind(server_fd, (struct sockaddr*)&server_addr, sizeof(server_addr));

    // 监听连接
    int receive_fd = listen(server_fd, 5); 

    std::cout << "Server listening on port 8888..." << std::endl;

    // 接受连接
    int client_fd = accept(server_fd, NULL, NULL);

    // 接收客户端消息
    char buffer[1024] = {0};
    recv(client_fd, buffer, 1024, 0); 
    std::cout << "Received message from client:\n " << buffer << std::endl;

    // 发送回复消息
    std::string reply = "Message received!";
    send(client_fd, reply.c_str(), reply.length(), 0); 

    // 关闭连接
    close(client_fd);
    close(server_fd);

    return 0;
}
```

使用命令 `g++ -std=c++11 server.cpp -o server` 生成可执行文件，然后 `./server` 运行；

新开一个窗口，写入客户端代码(client.cpp)：

```cpp
#include <iostream>
#include <string>
#include <sys/socket.h>
#include <netinet/in.h>
#include <unistd.h>
#include <arpa/inet.h>

int main() {
    // 创建套接字
    int client_fd = socket(AF_INET, SOCK_STREAM, 0); 

    // 连接服务器
    struct sockaddr_in server_addr;
    server_addr.sin_family = AF_INET;
    inet_pton(AF_INET, "116.204.72.210", &server_addr.sin_addr);
    server_addr.sin_port = htons(8888);
    connect(client_fd, (struct sockaddr*)&server_addr, sizeof(server_addr));

    std::cout << "Going to send message! \n";

    // 发送消息给服务器
    std::string message = "Hello, this is a message from client! Happy new year!";
    send(client_fd, message.c_str(), message.length(), 0); 

    // 接收服务器回复
    char buffer[1024] = {0};
    recv(client_fd, buffer, 1024, 0); 
    std::cout << "Received reply from server:\n " << buffer << std::endl;

    // 关闭连接
    close(client_fd);

    return 0;
}
```

使用命令 `g++ -std=c++11 client.cpp -o client` 生成可执行文件，然后 `./client` 运行；

最后的运行结果如下：

<div align="left">    
<img src="/img/socket-server.png" alt="server" width="200%" height="200%">
</div>

<div align="left">    
<img src="/img/socket-client.png" alt="client" width="200%" height="200%">
</div>

-------------

**Tips**

使用云服务器执行上面的代码时，要记得去云服务器控制台的安全组里开放8888端口哦，不然是看不到上面的执行结果的。
