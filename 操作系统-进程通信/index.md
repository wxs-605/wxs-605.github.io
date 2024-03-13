# 操作系统 进程通信

今天详细学学Linux中的进程是如何通信的。总的来说，进程之间通信的方式有五种，分别是管道、消息队列、信号、信号量和共享内存。
<center>

![进程之间的通信方式](/img/linux-进程通信.png)

</center>

### 管道通信

管道通信是一种进程间通信的机制，用于在不同进程之间传递数据。在Linux和类Unix系统中，管道是一种特殊的文件描述符，它可以连接一个进程的输出和另一个进程的输入，从而实现进程间的单向通信。管道通信又分为匿名管道和有名管道。

#### 匿名管道
1. 只能用于具有亲缘关系的进程之间通信；
2. 是一个单工的通信模式，具有固定的读端和写端；
3. 管道也可以看成一种特殊文件，对他的读写可以使用 read()，write() 函数；

**前提知识**
1. `pipe()` 函数：`pipe()` 函数用于创建一个匿名管道，可以实现父子进程之间的单向通信。

2. `fork()` 函数：`fork()` 函数创建一个新的进程，新进程是调用进程的副本。在`fork()`函数被调用后，操作系统会复制当前进程的所有信息（包括代码段、数据段、堆栈等）给新进程，然后让新进程和原进程同时执行。`fork()`函数返回两次，一次在父进程中返回子进程的PID（进程ID），一次在子进程中返回0。

```c
#include <stdio.h>
#include <unistd.h>

int main(){
    int fd[2];
    pipe(fd);
    int pid = fork();
    if(pid == 0)//子进程
    {   
        close(fd[0]);   //子进程关闭读端
        printf("There is son!\n");
        write(fd[1], "Hello world!", sizeof("Hello world!"));
    }   
    else if(pid > 0)//父进程
    {   
        char buf[100] = { 0 };
        close(fd[1]);//父进程关闭写端
        read(fd[0], buf, sizeof(buf));//读出数据
        printf("There is father!\n");
        printf("read is %s\n", buf);//打印
    }   
}
```

将这段代码编译运行一下，可以得到以下结果：
<center>

![验证匿名管道](/img/验证匿名管道.png)

</center>

#### 命名管道
1. 可以使两个相关的进程实现彼此通信；
2. 该管道可通过路径名指出，在文件系统中可见；
3. 严格遵守先进先出的规则，在开始处读数据，末尾处写数据；

**前提知识**
`mkfifo()` 函数是用于创建命名管道（FIFO）的函数。FIFO 是一种特殊类型的文件，在文件系统中有对应的路径名，可以用于无关联进程之间的通信。`mkfifo()` 函数创建一个新的FIFO 文件，并为其指定一个路径名。
```c
int mkfifo(const char *pathname, mode_t mode);
```

- pathname：要穿件的FIFO路径名；
- mode：设置FIFO的权限模式，用八进制表示

调用`mkfifo()` 函数后，将在指定路径上创建一个新的 FIFO 文件。其他进程可以通过打开这个文件来进行读取或写入操作，实现进程间的通信。在创建 FIFO 文件后，可以像操作普通文件一样对其进行读写操作。此外，`mkfifo()`函数创建的FIFO文件不会自动删除，需要收到删除，可以调用`unlink()`函数来删除。

在Linux系统中，文件的模式有以下几种

模式|所有者|所属者|其他用户
-|:-:|:-:|:-:
读权限|4|4|4
读和写权限|6|6|6
读、写和执行权限|7|7|7
读和执行权限|5|5|5
写和执行权限|3|3|3
写权限|2|2|2
执行权限|1|1|1
没有任何权限|0|0|0


```c
// 读端
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
int main()
{
    mkfifo("myfifo", 0666);    
    int fd = open("myfifo", O_RDWR);
    if(fd > 0)
    {   
        char buf[100] = { 0 };
        read(fd, buf, sizeof(buf));
        printf("读取数据。。。\n");
        printf("%s\n", buf);
        close(fd);
    }   
}

// 写端
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
int main()
{
    int fd = open("myfifo", O_RDWR);
    if(fd > 0)
    {   
        char message[] = "Hello, this is a message for the FIFO!\n";
        write(fd, message, sizeof(message));
        close(fd);
    }   
}
```
将者两段代码分别编译运行以下，可得到以下结果：
<center>

![验证命名管道](/img/验证命名管道.png)

</center>

剩下的例子改天再写，卷不动了。。。

