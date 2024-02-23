# CPP 线程管理


每个程序至少有一个线程：执行 `main()` 函数的线程，其余线程有其各自的入口函数。线程与原始线程(以main()为入口函数的线程)同时运行。

### 线程在什么时候启动?
线程在`std::thread` 对象创建时启动。启动线程时，可以调用可调用类型构造，有以下几种方式：

- 直接传入函数;

```cpp
#include <iostream>
#include <thread>

using namespace std;

void hello(){
    for(int i = 0; i <= 50;i++)
        std::cout << "Hello this is new thread!\n";
}

int main(){
    std::thread t(hello);
    t.detach();
    for(int i = 0; i <10000;i++);
}
```
如上面的`hello()`函数，启动线程时直接传入；

- 构造一个类，类中调用相关函数:

```cpp
#include <iostream>
#include <thread>

using namespace std;

void hello(){
    for(int i = 0; i <= 50;i++)
        std::cout << "Hello this is new thread!\n";
}

class task{
    public:
        void operator()() const{
            hello();
        }   
};

int main(){
    task t;
    std::thread my_thread(t);
    my_thread.join();
} 
```

构造了一个task类，类里面调用了 `hello()` 函数；

- 使用lambda表达式传递函数给线程:

```cpp
#include <iostream>
#include <thread>

using namespace std;

void hello(){
    for(int i = 0; i <= 50;i++)
        std::cout << "Hello this is new thread!\n";
}

int main(){
    std::thread my_thread([]{
        hello();
    }); 
    my_thread.join();
}
```

使用lambda表达式，里面调用了 `hello()` 函数；

### join() 与 detach() 的区别？

#### thread.join()

`thread.join()`是一个线程控制的方法，用于等待线程执行结束并回收线程资源。当调用 `thread.join()` 时，当前线程会被阻塞，直到被调用 `join()` 的线程执行结束。**比如** 线程 A 调用了线程 B 的 `join()` 方法，那么线程 A 将会一直等待，直到线程 B 执行完毕。在这期间，线程 A 处于阻塞状态，不会继续执行后面的代码，直到线程 B 完成任务。

`join()` 方法通常用于协调多个线程的执行顺序，可以确保在主线程中等待子线程执行完毕后再继续执行后续的操作。这对于需要线程间同步和协作的情况非常有用。

---------

#### thread.detach()

`thread.detach()` 是一个线程控制的方法，它用于将当前线程与其底层的操作系统线程分离，使得当前线程无法再被控制。调用 `detach()` 后，当前线程就成为了一个独立的线程，它不再与主线程或其他线程同步，也不能被等待或取消。该线程将会继续独立运行，直到线程执行结束或进程退出。

`detach()` 方法通常用于那些需要长时间运行的线程，例如网络服务或后台计算任务。在这种情况下，线程可以在后台运行，并且不需要等待主线程或其他线程的控制。使用 `detach()` 可以确保线程的独立执行，从而提高程序的并发性和效率。

但是，使用 `detach()` 方法时要小心避免线程资源泄露的情况，即在线程执行结束前，没有及时释放相关的资源，导致程序出现内存泄漏等问题。

**注意**

只能对一个线程执行一次 `join()` 或者 `detach()` 操作，否则会发生错误，可以通过 `thread.joinable()` 函数的返回值来判断，如果返回值是 `true` ，则可以执行一次`join()` 或者 `detach()` 操作，否则不能执行。

---------------

### 如何向线程函数传递参数？

- 在创建线程时，在线程函数后面跟上需要传递的参数:

```cpp
#include <iostream>
#include <thread>

using namespace std;

void hello(int a, int b){ 
    std::cout << a << " " << b << endl;
    for(int i = 0; i <= 50;i++)
        std::cout << "Hello this is new thread!\n";
}

int main(){
    std::thread my_thread(hello, 11,33);
    my_thread.join();
}
```

注意：当使用引用传参时，如果传入的是一个局部变量，有可能当前线程执行完毕，局部变量被销毁，而创建的线程里使用了这个局部变量，会造成错误。此外， `std::thread` 的构造函数会无视线程函数期待的参数类型，并盲目的拷贝已提供的变量。所以当使用引用传参时，用 `std::ref` 将参数转换成引用的形式.

- 构造可调用类，传递给线程函数:

```cpp
#include <iostream>
#include <thread>

using namespace std;

void hello(int a, int b){ 
    std::cout << a << " " << b << endl;
    for(int i = 0; i <= 50;i++)
        std::cout << "Hello this is new thread!\n";
}

class task{
    public:
        void do_something(int a, int b){ 
            hello(a, b); 
        }   
};

int main(){
    task t;
    int a(11);
    int b(22);
    std::thread my_thread(&task::do_something, &t, a, b); 
    my_thread.join();
}
```

第一个是传递给线程的参数，第二个是实例化的类的对象，第三个开始就是线程函数需要的参数；

---------

### 如何运行时决定线程的数量？

使用 `std::thread::hardware_concurrency()` 函数，它是 C++ 标准库中的一个函数，它返回当前系统支持的并发线程数目。这个函数通常用于帮助程序在运行时确定最佳的线程数量，以便充分利用系统的硬件资源来实现并行计算。`std::thread::hardware_concurrency()` 返回一个表示系统支持的并发线程数目的整数值。这个值可以帮助程序在运行时动态地确定需要创建多少个线程来执行并行任务，以便充分利用多核 CPU 或多线程处理器的优势。

在实际应用中，可以使用 `std::thread::hardware_concurrency()` 来获取系统的并发线程数目，并根据这个值来动态地调整并行计算任务的线程数。这样可以避免创建过多线程导致资源浪费，也可以避免创建过少线程导致无法充分利用系统的硬件资源。

但是，`std::thread::hardware_concurrency()` 返回的值并不是固定不变的，它可以随着系统配置和运行时环境的变化而变化。因此，在使用该函数时，需要考虑到系统可能的变化，并灵活地调整程序的并发策略。

---------

### 如何识别线程？

线程如同进程有PID一样，线程也有线程ID，其标识类型是 `std::thread::id` 。获取线程ID的方式有两种：
1. 通过 `std::thread` 对象的成员函数 `get_id()` 来直接获取，如果其没有关联执行线程，则返回默认构造值，表示没有这个线程；
2. 当前线程可通过调用 `std::this_thread::get_id()` 获得当前线程的ID；

`std::thread::id` 对象可以自由的拷贝和对比,因为标识符可以复用。如果两个对象的 `std::thread::id` 相等，那它们就是同一个线程，或者都“没有线程”。`std::thread::id` 对象可以作为容器排序、存储的键值。线程ID一般用来识别不同的线程，让他们完成不同的工作。

```cpp
#include <iostream>
#include <thread>

using namespace std;

void hello(int a, int b){ 
    std::cout << a << " " << b << endl;
    std::cout << std::this_thread::get_id() << endl;
    for(int i = 0; i <= 3;i++)
        std::cout << "Hello this is new thread!\n";
}

class task{
    public:
        void do_something(int a, int b){ 
            hello(a, b); 
        }   
};

int main(){
    std::cout << std::this_thread::get_id() << endl;
    task t;
    int a(11);
    int b(22);
    std::thread my_thread(&task::do_something, &t, a, b); 
    std::cout << my_thread.get_id() << endl;
    my_thread.join();
}
```

