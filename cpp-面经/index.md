# CPP 面经

*写在前头*

这篇文章用来记录面试过程中的一些题目，以及自己在面试过程中请教HR指出的自己的不足之处，均在此记录，加以改正！

## 公司一

这家公司的技术面问了很多操作系统方面的知识，还有部分计网知识，没问我项目和C++方面的东西，最后有一道手撕代码，LeetCode上的简单题。

主管面的话结果没给我，但我估计已经凉了。也确实，这次主管面HR给了我5分钟自我介绍，但我说的很简略。还有项目介绍方面，我一上来就讲了我做了什么东西，把HR讲的云里雾里的，逻辑不清。并且面试前一定要充分了解好这家公司，包括你入职之后会做什么等。这些是最后反问环节我主动询问HR，HR向我反馈的，要加以改正。

### 技术面

#### 自我介绍

#### 进程跟线程的区别？
线程（Thread）和进程（Process）是操作系统中的两个基本概念，它们有以下区别：

1. 资源占用：一个进程是一个独立的执行环境，拥有自己的地址空间、文件描述符、打开的文件等资源。而线程是进程内的一个执行单元，共享同一个地址空间和其他资源。

2. 执行单位：进程是一个独立的执行单位，拥有独立的代码和数据。而线程是进程内的执行流，可以看作是轻量级的进程，共享进程的代码和数据。

3. 切换开销：由于线程共享进程的资源，线程之间的切换开销相对较小。而进程之间的切换需要保存和恢复更多的状态信息，开销相对较大。

4. 通信方式：进程之间的通信需要使用特定的机制，如管道、消息队列、共享内存等。而线程之间可以直接通过共享内存和全局变量等进行通信，因为它们共享同一地址空间。

5. 并发性：由于线程共享进程的资源，多线程程序可以实现更高的并发性。而多进程程序需要使用进程间通信机制来实现并发操作。

6. 容错性：一个线程的崩溃可能会导致整个进程的崩溃，因为它们共享同一地址空间。而多个进程之间相互独立，一个进程的崩溃不会影响其他进程的执行。

进程是操作系统中资源分配和调度的基本单位，而线程是进程内的执行单位，用于实现并发执行和提高程序的效率。线程在共享资源和通信方面具有优势，但需要更谨慎地处理并发访问的问题。进程之间的隔离性使其更容易实现容错和安全性。

#### 进程之间的通信方式？
1. 管道（Pipe）：管道是一种半双工的通信方式，它可以在具有亲缘关系的进程之间传递数据。管道可以分为匿名管道和命名管道，匿名管道通常用于父子进程之间的通信。

2. 共享内存（Shared Memory）：共享内存是一种高效的进程间通信方式，多个进程可以访问同一块物理内存区域，实现数据共享。需要注意的是，由于共享内存不提供同步机制，因此在使用时需要结合其他同步手段，如信号量。

3. 信号量（Semaphore）：信号量用于协调多个进程对共享资源的访问，它可以用作互斥锁或者计数器。通过对信号量的操作（P操作和V操作），进程可以实现对资源的互斥访问和同步。

4. 消息队列（Message Queue）：消息队列是一种有序的消息链表，进程可以通过消息队列向其他进程发送消息。每个消息都有一个类型标识，接收进程可以选择性地接收某种类型的消息。

#### 你了解中断吗？
中断是指硬件设备或软件系统向中央处理器发出信号，要求其暂停当前正在执行的任务，转而去处理新发生的事件。中断通常用于处理输入/输出请求、时钟中断、故障和异常等情况。中断分为内中断和外中断：

1. 内中断又称软件中断，是由CPU执行指令执行期间产生的中断信号，例如程序执行过程中遇到了除零操作、越界访问、栈溢出等异常情况。这种中断需要通过软件来处理，通常由操作系统内核来处理。内中断的处理是在CPU内部完成的，没有外部硬件设备的参与。

2. 外中断又称硬件中断，是由外部硬件设备向CPU发出的中断信号，例如键盘输入、鼠标移动、磁盘读写等操作。这种中断需要通过硬件来处理，通常由中断控制器来处理。外中断的处理是在CPU内部和外部硬件设备之间完成的，需要外部设备的参与。

#### 中断的处理流程？
中断的处理流程通常包括以下几个步骤：

1. 中断触发：当发生中断事件时，可以是内部产生的异常或外部设备发送的中断请求。例如，键盘输入事件或除零错误等。

2. 中断向量定位：CPU通过中断向量来确定中断类型和对应的中断处理程序的位置。中断向量是一个预定义的地址，记录了中断处理程序的入口点。

3. 保存上下文：在开始执行中断处理程序之前，CPU会保存当前的执行现场（即上下文），包括程序计数器、寄存器状态和其他必要的信息。这是为了确保在中断处理完成后能够恢复到原来的执行状态。

4. 转移控制权：CPU将控制权转移到中断处理程序的入口点，开始执行中断处理程序。这个过程通常涉及跳转指令或函数调用。

5. 中断处理：中断处理程序根据中断类型进行相应的处理。这可能涉及到与设备通信、数据处理、状态更新等操作，具体的处理逻辑根据不同的中断类型而异。

6. 恢复上下文：在中断处理完成后，CPU会恢复之前保存的上下文，包括程序计数器、寄存器状态等。这样可以继续执行被中断的程序，并确保程序的执行不受影响。

7. 中断返回：CPU将控制权从中断处理程序返回到被中断的程序，继续执行原来的指令序列。

#### 你了解死锁吗？死锁产生的原因是啥？
死锁是指两个或多个进程或线程在执行过程中，因争夺资源而造成的一种僵局状态。死锁产生的原因通常包括以下几个方面：

1. 互斥条件：进程对所分配到的资源进行排他性使用，即在一段时间内某资源只能被一个进程占有。当某进程申请新的资源而又因为其他进程占有而无法分配时，该进程会进入等待状态。

2. 请求与保持条件：已获得资源的进程可以继续申请新的资源，而不释放已占有的资源。如果此时有其他进程需要已占有的资源，则会造成资源竞争，导致死锁。

3. 不剥夺条件：进程已获得的资源在未使用完之前，不能被剥夺，只能在自己使用完时自行释放。这样做的目的是防止进程有可能永远等待下去。

4. 循环等待条件：存在一个进程等待队列{P1, P2, …, Pn}，其中P1等待P2所占有的资源，P2等待P3所占有的资源，……，Pn等待P1所占有的资源，形成一个进程循环等待环路。

#### 你有什么办法解决死锁吗？
1. 预防死锁：通过破坏死锁产生的四个条件之一，来预防死锁的发生。例如，只允许进程一口气获取它所需的全部资源，或者按照一定的顺序获取资源。

2. 避免死锁：在资源分配过程中，用某种方法避免系统进入不安全状态，避免死锁的产生。采用银行家算法判断是否存在安全序列；

3. 检测死锁：通过周期性地检测系统中是否存在死锁，来及时发现和解除死锁。检测死锁的方法有多种，例如使用资源分配图、使用等待-通知机制等。

4. 解除死锁：当检测到死锁时，采取一些措施来解除死锁。例如，通过抢占资源、回收资源、剥夺进程等方式来解除死锁。

#### 操作系统的内存管理？
内存管理的主要任务包括以下几个方面：

1. 内存分配：操作系统负责将可用的内存空间分配给进程。常见的内存分配方式有连续分配、非连续分配和虚拟内存技术等。

- 连续分配：将物理内存按照固定大小的块划分，并将这些块分配给进程。常见的连续分配算法有首次适应、最佳适应和最坏适应等。

- 非连续分配：将进程的地址空间划分为多个不连续的部分，并将这些部分映射到物理内存的不同区域。常见的非连续分配方式有页式和段式。

2. 虚拟内存：通过将进程的逻辑地址空间映射到物理内存，实现了比物理内存更大的地址空间。虚拟内存将内存分为固定大小的页，并根据需求将页加载到物理内存中。

3. 内存保护：操作系统通过内存保护机制来保护不同进程之间的内存空间，防止进程越界访问其他进程的内存。常见的内存保护机制有基址限制寄存器（Base and Limit Register）和分段机制等。

4. 内存回收：当一个进程结束或释放内存时，操作系统需要将其占用的内存空间回收并重新分配给其他进程。内存回收可以通过回收页、合并碎片等方式进行。

5. 虚拟内存管理：虚拟内存是一种扩展了物理内存的技术，它将物理内存和磁盘空间结合起来，提供了比物理内存更大的地址空间。操作系统负责将进程的虚拟地址映射到物理内存或者磁盘上，并负责进行页面置换、页面调度等管理操作。

#### 虚拟内存如何映射到物理内存？
虚拟内存是一种扩展了物理内存的技术，它将进程的逻辑地址空间映射到物理内存或磁盘上。虚拟内存的映射过程主要包括以下几个步骤：

1. 地址转换：当进程访问虚拟地址时，操作系统需要将虚拟地址转换成物理地址，以便正确地定位数据在内存中的位置。这个转换过程是通过硬件辅助的地址转换机制来完成的。进程切换时，进程的起始物理地址被放进 页表基址寄存器(PTBR)中。

2. 页表查找：操作系统维护了一个页表（Page Table），它记录了虚拟内存页面和物理内存页面之间的映射关系。当进行地址转换时，操作系统会根据虚拟地址的页表索引定位到相应的页表项。

3. 映射状态检查：在页表项中，记录了该虚拟页是否已经被映射到物理内存中，以及物理页的地址。操作系统会检查页表项的映射状态，如果虚拟页已经映射到物理内存中，则可以直接得到物理页的地址。

4. 缺页处理：如果虚拟页未映射到物理内存中，即发生了缺页（Page Fault），操作系统需要进行缺页处理。缺页处理的过程包括从磁盘上加载缺失的页面到物理内存中，并更新页表的映射关系。

- 页面置换：如果物理内存已满，操作系统需要选择一个页面进行置换，以腾出空间来加载新的页面。常见的页面置换算法有最佳置换算法、最近未使用（LRU）置换算法等。

- 页面调度：操作系统还可以根据页面的访问模式和访问频率进行页面调度，将经常被访问的页面保留在物理内存中，减少缺页的发生。

物理地址计算：一旦确定了虚拟地址对应的物理页，操作系统将通过页表项中的物理页地址和页内偏移量计算出真正的物理地址。

虚拟内存的映射过程是操作系统内存管理的核心部分，它通过将进程的逻辑地址空间映射到物理内存或磁盘上，提供了比实际物理内存更大的地址空间，提高了系统的灵活性和性能。

#### 页面置换算法有哪些？
1. 最佳置换算法（Optimal Replacement Algorithm）：最佳置换算法总是选择将未来最长时间不被访问的页面进行置换，即选择与当前页面距离最远的页面。这种算法的优点是能够保证在所有置换算法中产生的缺页次数最少，但是需要预测未来的页面访问模式，实现较为困难。

2. 先进先出置换算法（FIFO Replacement Algorithm）：先进先出置换算法总是选择最早进入内存的页面进行置换。这种算法的实现简单，但是存在一种称为“Belady异常”的现象，即当内存大小增加时，缺页次数反而会增加。

3. 最近最久未使用置换算法（LRU Replacement Algorithm）：最近最久未使用置换算法总是选择最近最久未被访问的页面进行置换。这种算法更加符合实际页面访问模式的特点，能够有效地减少缺页次数，但是实现相对复杂。

4. 时钟置换算法（Clock Replacement Algorithm）：时钟置换算法是一种简单的近似LRU算法，它使用一个环形链表来维护内存中页面的顺序。当需要进行页面置换时，时钟算法从当前指针位置开始扫描链表，将第一个未被访问的页面进行置换。

5. 最近未使用置换算法（NRU Replacement Algorithm）：最近未使用置换算法将内存中的页面分为四个类别，分别是已修改且最近未使用、已修改但最近使用、未修改且最近未使用、未修改但最近使用。在进行页面置换时，先选择未修改且最近未使用的页面进行置换，如果不存在，则选择未修改但最近使用的页面，以此类推。

如何管理内存空间？
1. 空闲列表（Free List）：操作系统通常会维护一个空闲列表，记录可用的内存块。当有进程请求内存时，操作系统会从空闲列表中找到一个足够大的内存块分配给该进程。空闲列表可以使用链表、位图或其他数据结构来实现。

2. 首次适应算法（First Fit Algorithm）：首次适应算法是一种简单的内存分配策略，它从空闲列表中找到第一个足够大的内存块分配给进程。这种算法的优点是分配速度快，但可能会导致内存碎片化问题。

3. 最佳适应算法（Best Fit Algorithm）：最佳适应算法选择空闲列表中最小的足够大的内存块进行分配。这种算法可以减少内存碎片化的问题，但需要遍历整个空闲列表，分配速度较慢。

4. 最差适应算法（Worst Fit Algorithm）：最差适应算法选择空闲列表中最大的足够大的内存块进行分配。这种算法可以减少大型内存块的浪费，但可能会导致较大的内存碎片化。

5. 快速适应算法（Quick Fit Algorithm）：快速适应算法是一种结合了首次适应和最佳适应算法的策略。它将内存划分为几个大小相等的块，并为每个块维护一个空闲列表。当有进程请求内存时，操作系统先在对应大小的块的空闲列表中找到合适的内存块进行分配。

6. 内存回收：当进程释放内存时，操作系统需要将该内存块标记为空闲，并将其加入到空闲列表中。如果相邻的空闲内存块存在，则可以考虑合并它们，以减少内存碎片。

管理空闲内存空间的目标是尽量提供连续、足够大的内存块来满足进程的需求，并尽量减少内存碎片化问题。不同的管理方法和策略在实际场景中有不同的适用性和效果，操作系统会根据具体情况选择合适的管理方式。

#### 你了解内存碎片吗？
内存碎片是指已分配的内存空间中存在未被使用或无法利用的小块零散内存。它可以分为两种类型：外部碎片和内部碎片。

外部碎片是指已分配的内存块之间的零散空闲内存，由于这些空闲内存不连续，无法被利用。而内部碎片是指已分配的内存块中有部分空间未被使用，导致浪费。

下面是一些常见的处理内存碎片问题的方法：

1. 内存紧缩（Memory Compaction）：内存紧缩是一种重排已分配内存块的方法，将所有已分配的内存块向一端移动，以便形成一个较大的连续内存块。这样可以解决外部碎片问题，并提供更大的连续内存空间。

2. 分区合并（Partition Coalescing）：当有连续的空闲内存块相邻时，可以将它们合并成一个更大的空闲内存块，从而减少外部碎片。这通常在内存回收时进行。

3. 动态分区分配（Dynamic Partitioning）：动态分区分配是一种在运行时根据需求动态地分割和合并内存块的方法。它使得每个进程可以根据需要获得一个合适大小的内存块，从而减少内部碎片。

4. 使用伙伴系统（Buddy System）：伙伴系统将内存划分为大小相等的块，并以2的幂次作为划分依据。当有进程请求内存时，系统会分配最接近所需大小的一个块，如果剩余空间足够大，则会将其分割为两个大小相等的块。这种方法可以减少外部碎片，并保持内存的整齐性。

5. 使用内存池（Memory Pool）：内存池是一种预先分配一定数量的固定大小的内存块，并在需要时直接从池中获取内存。使用内存池可以减少内部碎片，提高内存分配效率。

6. 垃圾回收（Garbage Collection）：对于使用垃圾回收的环境，内存管理由垃圾收集器负责。垃圾收集器会自动回收不再使用的内存，从而减少碎片化问题。

** 当我申请一块内存空间时，为什么系统有足够大的空间却申请失败？

因为存在了大量的外部碎片，导致无法申请一块满足条件的连续的内存块。

#### TCP与UDP的区别？
TCP（Transmission Control Protocol）和UDP（User Datagram Protocol）是两种常见的传输层协议，它们有以下的区别：

1. 连接方式：TCP是面向连接的协议，而UDP是无连接的协议。TCP在传输数据之前需要先建立连接，而UDP可以直接发送数据，不需要先建立连接。

2. 可靠性：TCP是一种可靠的协议，它保证数据的传输是无差错、无丢失、有序的。而UDP不保证数据的可靠性，它可能会出现丢包、重复、乱序等情况。

3. 数据量：TCP支持大数据量的传输，它会把数据分成多个TCP段进行传输。而UDP每次只能传输少量的数据，因为它发送的数据报文大小受限于网络MTU。

4. 延迟和吞吐量：TCP对延迟和吞吐量的处理较为谨慎，它会采取一些措施来防止网络拥塞。而UDP则不会对网络拥塞进行控制，因此它的延迟较低，但吞吐量可能会受到影响。

5. 应用场景：TCP适用于要求数据可靠传输的应用，如网页浏览、文件下载、邮件传输等。而UDP适用于实时数据传输的应用，如音视频传输、游戏数据传输等。

#### TCP的三次握手？
1. 第一步：客户端发送连接请求
客户端向服务器发送一个特殊的TCP报文段，该报文段中标记为SYN（同步）位，并选择一个随机的初始序列号（ISN）作为起始序列号。这个报文段用于表明客户端希望与服务器建立连接。

2. 第二步：服务器回应连接请求
服务器收到客户端发送的TCP报文段后，如果同意建立连接，就会向客户端发送一个特殊的TCP报文段作为响应。在这个响应报文段中，服务器将确认ACK位和SYN位都设置为1，同时选择自己的初始序列号（ISN），并将客户端的序列号加1作为确认号。

3. 第三步：客户端确认连接请求
客户端接收到服务器发送的TCP报文段后，会对其进行确认。客户端向服务器发送一个特殊的TCP报文段，其中ACK位设置为1，表示确认收到了服务器的响应。客户端的序列号加1作为确认号。及三次握手客户端可以发送数据。

至此，TCP的三次握手完成，连接成功建立。此后，客户端和服务器可以开始进行数据传输。

三次握手的目的是确保两端都能够正常发送和接收数据，以建立可靠的连接。通过三次握手，可以避免已失效的连接请求报文段被服务器错误地接受，从而确保了连接的可靠性和稳定性。

#### 手撕代码

[√](https://leetcode.cn/problems/sort-array-by-increasing-frequency/description/)

自定义cmp，调用库函数中的sort即可。

### 主管面

#### 自我介绍

#### 问项目

#### 你接受加班吗？你加班的上限是什么？

#### 在项目合作开发中，你觉得怎么样才能更好的合作，或者说你学到了什么？

#### 你最熟悉哪个项目？写了多少行代码？

总结，最后面试官说我准备不充分，自我介绍没有展示自己的优势，回答的太简略，项目介绍方面，不要一上来就说你做了什么，要先说项目的背景，存在的问题，业界怎么做的，你是怎么解决的，你做了什么。


