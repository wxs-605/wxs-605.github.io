# CPP 智能指针


就像谈到提起北京就会说到故宫一样，学习 C++ 时自然而然会提及智能指针，在 C++98 时推出了 `auto_ptr` ，在C++11时智能指针的功能更加完善，推出了更加安全和多功能的智能指针，同时弃用了首次提出的不严谨的 `quto_ptr`。

## 智能指针介绍

C++里面的四个智能指针: `auto_ptr`, `unique_ptr`, `shared_ptr`, `weak_ptr` 其中后三个是C++11支持，并且第一个已经被C++11弃用。使用时需要引入头文件  `#include <memory>`。

智能指针主要用于管理在堆上分配的内存，它将普通的指针封装为一个类对象，对象在栈中定义。当类对象的生存周期结束后，会调用对象的析构函数，在析构函数中释放掉申请的内存，从而防止内存泄漏。智能指针实质是一个对象，行为表现的却像一个指针。


### auto_ptr

（C++98的方案，C++11已经抛弃）采用所有权模式。
```cpp
auto_ptr<string> p (new string ("I reigned lonely as a cloud.")); 

auto_ptr<string> q; 

q = p; //auto_ptr不会报错.
```
此时不会报错，q剥夺了p的所有权，但是当程序运行时访问p将会程序崩溃。所以 `auto_ptr` 的缺点是：它允许拷贝，但是拷贝后再次访问原对象会导致程序崩溃，而 `unique_ptr`则会在编译过程报错。C++11中 `auto_ptr` 被摒弃，取而代之的是 `unique_ptr` 。



### unique_ptr

`unique_ptr` 实现独占式拥有或严格拥有概念，保证同一时间内只有一个智能指针可以指向该对象，是 `auto_ptr` 的代替方案。`unique_ptr`的资源管理权不能赋值和分享，只能转移所有权力， 它对于避免资源泄露(例如“以new创建对象后因为发生异常而忘记调用delete”)特别有用。`unique_ptr` 与原始指针一样有效，并可用于STL容器。

`unique_ptr`的实现方式是将类的拷贝构造函数、无参构造函数和赋值操作符都声明为 `delete` 或 `private`

```cpp
unique_ptr<string> p (new string ("auto"));   

unique_ptr<string> q；

q = p;//此时会报错！！
```
编译器认为 q = p 非法，避免了 p 不再指向有效数据的问题。尝试复制 p 时会编译期出错，而auto_ptr能通过编译期从而在运行期埋下出错的隐患。因此，unique_ptr比auto_ptr更安全。

#### unique_ptr常用操作

```cpp
unique_ptr<T> u1; // 空unique_ptr，可以指向类型为T的对象。u1会使用delete来释放它的指针

unique_ptr<T, D> u2; // u2会使用一个类型为D的可调用对象来释放它的指针，D也叫做自定义删除器

unique_ptr<T, D> u(d) // 空unique_ptr，指向类型为T的对象，用类型为D的对象d替代delete

u = nullptr // 释放u指向的对象，将u置为空

u.release() // u放弃对指针的控制权，返回指针，并将u置为空

u.reset() // 释放u指向的对象

u.reset(q) // 如果提供了内置指针q，另u指向这个对象；否则将u置为空

u.reset(nullptr)  
```
虽然我们不能拷贝或赋值unique_ptr，但可以通过调用 release 或 reset 将指针的所有权从一个（非const）unique_ptr转移给另一个unique_ptr；



### shared_ptr

`shared_ptr` 实现共享式拥有概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。从名字 share 就可以看出了资源可以被多个指针共享，它使用计数机制来表明资源被几个指针共享。可以通过成员函数 use_count() 来查看资源的所有者个数。除了可以通过new来构造，还可以通过传入  `unique_ptr`,`weak_ptr` 来构造。当我们调用 `release()` 时，当前指针会释放资源所有权，计数减一。当计数等于0时，资源会被释放。

`shared_ptr` 是为了解决 `auto_ptr` 在对象所有权上的局限性(auto_ptr 是独占的), 在使用引用计数的机制上提供了可以共享所有权的智能指针。

`shared_ptr` 的成员函数：
```cpp
shared_ptr<T>：//类模板，用于定义一个shared_ptr对象，其中T表示所指向对象的类型。

shared_ptr<T>::shared_ptr()：默认构造函数，创建一个空的shared_ptr对象。

shared_ptr<T>::shared_ptr(T* ptr)：构造函数，创建一个shared_ptr对象并初始化为指向ptr所指向的对象。

shared_ptr<T>::shared_ptr(const shared_ptr<T>& other)：拷贝构造函数，创建一个新的shared_ptr对象，并与other共享同一对象。

shared_ptr<T>::operator= (const shared_ptr<T>& other)：赋值运算符重载，将当前的shared_ptr对象与other共享同一对象。

shared_ptr<T>::reset()：重置shared_ptr对象，将其置为空指针。

shared_ptr<T>::get()：获取shared_ptr所指向的原始指针。

shared_ptr<T>::use_count()：获取当前shared_ptr对象的引用计数，即共享该对象的shared_ptr对象的数量。

shared_ptr<T>::unique()：判断当前shared_ptr对象是否是唯一拥有所指对象的智能指针。

shared_ptr<T>::operator bool() const：将shared_ptr对象转换为布尔值，判断其是否为空指针。

shared_ptr<T>::operator* ()：重载解引用操作符，返回所指对象的引用。

shared_ptr<T>::operator-> ()：重载箭头操作符，返回所指对象的指针。

shared_ptr<T>::swap(shared_ptr<T>& other)：交换两个shared_ptr对象的内容。

```


#### `shared_ptr` 的初始化
最安全的分配和使用动态内存的方法是调用一个名为 `make_shared` 的标准库函数。此函数在动态内存中分配一个对象并初始化它，返回指向此对象的 `shared_ptr`。与智能指针一样，`make_shared` 也定义在头文件 `memory` 中。
```cpp
// p 指向一个值为42的 int 的 shared_ptr 
shared_ptr<int> p = make_shared<int>(42);

// q 指向一个值为"9999999999"的 string
shared_ptr<string> q = make_shared<string>(10,'9');

// p5指向一个只初始化的int
shared_ptr<int> p5 = make_shared<int>();
```
我们还可以用 new 返回的指针来初始化智能指针，不过接受指针参数的智能指针构造函数是 explicit 的。因此，我们不能将一个内置指针隐式转换为一个智能指针，必须使用直接初始化形式来初始化一个智能指针；

```cpp
shared_ptr<int> pi = new int (1024); // 错误：必须使用直接初始化形式
shared_ptr<int> p2(new int(1024));    // 正确：使用了直接初始化形式
```

#### `shared_ptr`的底层原理？

<center>

![shared_ptr 底层原理](/img/sharedPtr.png)

</center>

`std::shared_ptr` 在内部维护一个引用计数，其只有两个指针成员，一个指针是所管理的数据的地址；还有一个指针是控制块的地址，包括引用计数、`weak_ptr` 计数、删除器(Deleter)、分配器(Allocator)。不同 `shared_ptr` 指针需要共享相同的内存对象，因此引用计数的存储是在堆上的。而 `unique_ptr` 只有一个指针成员，指向所管理的数据的地址。因此一个 `shared_ptr` 对象的大小是 裸指针 `unique_ptr` 大小的两倍。
- 删除器：自定义删除器的作用是在智能指针释放所管理的对象时，执行一些特殊的操作，比如释放资源时打印日志，管理除了内存以外的资源，如文件句柄。自定义删除器可以是一个函数，也可以是一个类的对象, 也可以是一个lambda表达式。
```cpp
// 函数形式的删除器
void free_memory(int* p) {
    std::cout << "delete memory" << std::endl;
    delete p;
}

// lambda表达式形式的删除器
auto free_memory_lambda = [](int* p) {
    std::cout << "delete memory" << std::endl;
    delete p;
}

// 类类型的删除器
class FreeMemory {
public:
    void operator()(int* p) {
        std::cout << "delete memory" << std::endl;
        delete p;
    }
};

std::shared_ptr<int> sp1(new int(0), free_memory); // size: 8
std::shared_ptr<int> sp2(new int(0), FreeMemory()); // size: 8
std::shared_ptr<int> sp3(new int(0), free_memory_lambda); // size: 8
```

一个简化版的智能指针实现，不考虑线程安全、删除器和分配器
```cpp

```


#### `shared_ptr`的引用计数如何实现，手写一个 `shared_ptr` ?

引用计数本身也是使用指针实现的，将计数的变量存储在堆上，所有共享指针的 `shared_ptr`就存储一个指向堆内存的指针。

### weak_ptr 

share_ptr虽然已经很好用了，但是有一点share_ptr智能指针还是有内存泄露的情况，当两个对象相互使用一个shared_ptr成员变量指向对方，会造成循环引用，使引用计数失效，从而导致内存泄漏。

weak_ptr 是一种不控制对象生命周期的智能指针, 它指向一个 shared_ptr 管理的对象. 进行该对象的内存管理的是那个强引用的shared_ptr， weak_ptr只是提供了对管理对象的一个访问手段。weak_ptr 设计的目的是为配合 shared_ptr 而引入的一种智能指针来协助 shared_ptr 工作, 它只可以从一个 shared_ptr 或另一个 weak_ptr 对象构造, 它的构造和析构不会引起引用记数的增加或减少。weak_ptr是用来解决shared_ptr相互引用时的死锁问题,如果说两个shared_ptr相互引用,那么这两个指针的引用计数永远不可能下降为0,资源永远不会释放。它是对对象的一种弱引用，不会增加对象的引用计数，和shared_ptr之间可以相互转化，shared_ptr可以直接赋值给它，它可以通过调用lock函数来获得shared_ptr；
