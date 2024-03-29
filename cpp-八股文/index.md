# CPP 八股文


参考文章：https://blog.csdn.net/Awesomewan/article/details/123948929 [链接](https://blog.csdn.net/Awesomewan/article/details/123948929)

参考公众号：阿Q技术站

参考博客：编程指北
链接: https://csguide.cn/cpp/object_oriented/virtual_function.html#c-%E5%AF%B9%E8%B1%A1%E6%A8%A1%E5%9E%8B
来源: https://csguide.cn


## 变量初始化/虚函数/多态

### 全局静态变量和函数静态变量的初始化顺序？
1. 全局静态变量的初始化顺序：
- 在同一个编译单元中，全局静态变量的初始化顺序按照它们在代码中的出现顺序进行；
- 在不同的编译单元中，全局静态变量的初始化顺序是不确定的，取决于编译器和链接器的实现。

2. 函数静态变量的初始化顺序：
- 函数内部的静态变量的初始化顺序与它们在函数内部的声明顺序相同；
- 函数内部的静态变量只会在第一次调用函数时初始化，之后的调用不会再重新初始化；

### 谈一下你对多态的理解？
多态是指允许不同的子类型的对象对同一消息做出不同的响应，也可简单的概括为“一个接口，多种方法”，C++多态主要是通过虚函数实现的，虚函数允许子类重写。多态有两种，分别是编译时多态和运行时多态，也叫静态多态和动态多态。

1. 编译时多态(静态多态，编译阶段，地址早绑定)：
静态多态通过函数重载和运算符重载来实现。在编译时，编译器根据调用的函数或运算符的参数类型和个数来确定具体调用哪个函数或运算符，这就是编译时多态。比如我有两个同名的 add 函数，第一个 add 函数的参数类型是 int 型，第二个 add 函数的参数类型是 float 型，那么我传入两个 int 型的参数到 add 函数中，则其回调用第一个 add 函数，返回一个 int 型的结果。

2. 运行时多态(动态多态，运行阶段，地址晚绑定)：
运行时多态通过继承和虚函数来实现。在运行时，程序根据对象的实际类型来确定调用哪个函数。在 C++ 中，通过在基类中声名虚函数，在派生类中重写这些虚函数，然后通过基类的指针或引用来调用这些函数，就可实现动态多态。
```cpp
class Fruit{
public:
	virtual void fruitName(){cout << "I am ..\n" << endl;}
};

class Apple : public Fruit{
public:
	void fruitName(){cout << "I am Apple!\n" << endl;}
};

int main(){
	Fruit* fruit1 = new Apple();

	fruit1->fruitName();	// print I am Apple!

	delete fruit1;

	return 0;
}
```

### 虚函数表的位置？
在C++中，虚函数表(virtual function table ， 简称vtable)适用于实现动态多态的一种机制，它存储了类的虚函数的地址，虚函数表通常是针对每个包含虚函数的类生成的，每个对象的内存中都有一个指向其所属类的虚函数表的指针，，用于在运行时确定调用哪个函数。

虚函数表的存储位置通常是在每个对象的内存布局中，具体位置取决于编译器和操作系统的是实现。一般来说，虚函数表位于对象的内存布局的起始位置，但并不是标准规定。虚函数表本身是一个指针数组，其中存储了指向每个虚函数的指针。

```
 +----------------+
  | 虚函数表指针   |  --> 指向虚函数表的指针
  +----------------+
  | 其他成员变量   |  --> 类的其他成员变量
  +----------------+

  虚函数表：
  +----------------+
  | 虚函数指针 1   |  --> 指向第一个虚函数的指针
  +----------------+
  | 虚函数指针 2   |  --> 指向第二个虚函数的指针
  +----------------+
  | ...            |
  +----------------+
```

示意图中，一个对象的内存布局中包括了一个指向虚函数表的指针和其他成员变量。虚函数表本身是一个指针数组，每个元素指向对应虚函数的实际实现，当调用一个虚函数时，程序会根据对象的虚函数表指针找到对应的虚函数表，然后根据函数在虚函数表中的索引找到实际的函数进行调用。

### 运行时刻能把虚函数表拿出来吗？
在程序运行时，虚函数表是存储在内存中的，但是，C++并没有提供直接的语言级别的手段来访问或操作虚函数表。虚函数表的内部结构和存储位置是由编译器决定，不同的编译器和不同的平台可能有不同的实现方式。

虚函数表存储在对象的内存布局中，一般作为对象的第一个成员，位于对象的起始位置处。这样做的目的是为了在运行时能够快速地找到对象所属类的虚函数表，从而实现动态多态性。每个包含虚函数的类都有自己的虚函数表，当对象被创建时，编译器会在对象的内存中插入一个指向类的虚函数表的指针(虚指针)，用于在运行时动态绑定。

### 拿到虚函数表的地址后，是否可以改写虚函数表的内容？
一般情况下，虚函数表是存储在程序的只读数据段中的，这意味着虚函数表的内容是只读的，不能被修改。如果尝试修改只读数据段的内容，通常会导致操作系统抛出段错误，或访问权限错误，从而导致程序异常终止。

### 对虚函数表的另一种看法
虚函数是通过一张虚函数表Virtual Table）来实现的，简称为V-Table。在这个表中，存放的是一个类的虚函数的地址表，这张表解决了继承、覆盖的问题，保证其真实反应实际的函数。

这个类的实例内存中都有一个虚函数表的指针，当我们用父类的指针来操作一个子类的时候，这张虚函数表指明了实际所应该调用的函数。

一个对象在内存中一般由成员变量（非静态）、虚函数表指针(vptr)构成。虚函数表指针指向一个数组，数组的元素就是各个虚函数的地址，通过函数的索引，我们就能直接访问对应的虚函数。

更底层一点，当基类指针或引用指向一个派生类对象时，调用虚函数时，实际上会调用派生类中的虚函数，而不是基类中的虚函数。**当一个类声明一个虚函数时，编译器会为该类创建一个虚函数表（Virtual Table）。**这个表存储着该类的虚函数指针，这些指针指向实际实现该虚函数的代码地址。

**每个对象**都包含一个指向该类的虚函数表的指针，这个指针**在对象创建时被初始化**，通常是作为对象的**第一个成员变量**。

当调用一个虚函数时，**编译器**会通过对象的**虚函数指针**查找到该对象所属的类的**虚函数表**，并根据函数的索引值（通常是函数在表中的位置，编译时就能确定）来找到对应的虚函数地址。

然后将控制转移到该地址，实际执行该函数的代码。对于派生类，其虚函数表通常是**在基类的虚函数表的基础上扩展而来**的。在派生类中，如果**重写了基类的虚函数，那么该函数在派生类的虚函数表中的地址会被更新为指向派生类中实际实现该函数的代码地址。**

代码源自 编程指北
```cpp
#include <iostream>

// 函数指针
typedef void(*Func)(void);

class MyBaseClass {
public:
    virtual void virtualMethod1() {
        std::cout << "BaseClass::virtualMethod1()" << std::endl;
    }
    virtual void virtualMethod2() {
        std::cout << "BaseClass::virtualMethod2()" << std::endl;
    }
    virtual void virtualMethod3() {
        std::cout << "BaseClass::virtualMethod3()" << std::endl;
    }

};

class MyDerivedClass : public MyBaseClass {
public:
    virtual void virtualMethod3() {
        std::cout << "DerivedClass::virtualMethod3()" << std::endl;
    }
    virtual void virtualMethod4() {
        std::cout << "DerivedClass::virtualMethod4()" << std::endl;
    }
    virtual void virtualMethod5() {
        std::cout << "DerivedClass::virtualMethod5()" << std::endl;
    }
};

void PrintVtable(void** vtable) {
    // 输出虚函数表中每个函数的地址
    for (int i = 0; vtable[i] != nullptr; i++) {
        // 最多调用五个函数，怕访问到虚函数表非法的地址，因为就五个函数
        if (i >= 5)  {
            return;
        }
        std::cout << "Function " << i << ": " << vtable[i] << std::endl;
        // 将虚函数表中的虚函数转换为函数指针，然后进行调用
        Func func = (Func) vtable[i];
        func();
    }
}

int main() {
    MyDerivedClass obj;

    // 取对象的首地址，然后转换为的指针，就取到了虚函数表指针，指向 obj 对象的虚函数表
    // 因为大多数实现上，虚函数表指针一般都放在对象第一个位置
    void** vtable = *(void***)(&obj);
    std::cout << "DerivedClass Vetable:" << std::endl;
    // 打印子类的虚函数表
    PrintVtable(vtable);

    std::cout << std::endl <<  "BaseClass Vetable:" << std::endl;
    MyBaseClass base_obj;
    void** vbtable = *(void***)(&base_obj);
    // 打印父类的虚函数表
    PrintVtable(vbtable);
    return 0;
}
```
本机执行结果
```
DerivedClass Vetable:
Function 0: 0x402e30
BaseClass::virtualMethod1()
Function 1: 0x402e70
BaseClass::virtualMethod2()
Function 2: 0x402ef0
DerivedClass::virtualMethod3()
Function 3: 0x402f30
DerivedClass::virtualMethod4()
Function 4: 0x402f70
DerivedClass::virtualMethod5()

BaseClass Vetable:
Function 0: 0x402e30
BaseClass::virtualMethod1()
Function 1: 0x402e70
BaseClass::virtualMethod2()
Function 2: 0x402eb0
BaseClass::virtualMethod3()
PS D:\code\cpp> 
```


## 基础知识  联系 <=> 区别

### 指针和引用的区别？
1. 指针是一个变量，存储的是一个地址，指向内存的一个单元；引用跟原来的变量是同一个东西，是原变量的别名；
2. 引用可以为空，被创建时必须初始化，并且初始化后不能改变；指针可以是空值，可以在任何时候初始化，初始化后可以指向其他的内存单元；
3. 指针可以加 const 修饰，但引用不能加 const ；
4. 可以有指针的指针，既多级指针，但没有引用的引用，引用只有一级；
5. sizeof(引用) 得到的是原变量的大小，sizeof(指针) 得到的是指针本身的大小；

### 堆区和栈区的区别？
1. 申请方式不同：栈是系统自动分配的，堆是程序员主动申请的；
2. 申请后的系统响应：栈里分配空间时，如果剩余空间大于申请空间则分配成功，否则分配失败栈溢出；申请堆空间时，在记录空闲地址空间的链表上寻找第一个大于申请空间的节点分配给程序，然后将该节点在空闲链表上删除，大多数系统中该块空间的首地址存放的是本次分配空间的大小，便于释放，将该块空间上的剩余剩余空间再次链接到空闲链表上；
3. 栈在内存中是一块连续的空间(向低地址扩展)，最大容量已经设定好，通常是64MB，堆在内存中的空间时不连续的(向高地址扩展)；
4. 申请效率：栈是系统自动分配，申请效率高，但程序员无法控制；堆是由程序员主动申请，效率低，使用起来方便的但是容易产生碎片；
5. 栈中存放的是局部变量，函数参数；堆中存放的内容由程序员控制；

### new 和 delete 是如何实现的？new 和 malloc 的异同？
1. new 和 delete 搭配使用，malloc 和 free 搭配使用；
2. malloc/free 是库函数，需要引入头文件 `#include<stdlib.h>`；new/delete 是关键字，需要编译器支持参数；
3. malloc 申请空间时需要确定申请的空间大小，new 申请空间时，无需指定分配空间大大小，编译器会根据类型自动计算；
4. maloc 申请空间时，返回的是 void* 类型，需要强制转换成对象类型的指针；new 申请空间时，返回的类型是对象的指针类型，无需强制类型转换；
5. malloc分配失败时，返回空指针，new 分配失败时，抛出 bad_alloc 异常；
6. 自定义类型：new 首先调用 operator new 函数申请空间(底层通过 malloc 实现)，然后调用对象的构造函数初始化，最后返回定义类型的指针；delete 先调用析构函数，然后调用operator delete 释放空间(底层通过 free )实现。malloc/free 不支持自定义类型的对象的析构和构造；
7. 使用 `malloc`分配内存后，如果在使用的过程中发现内存不足，可以使用 `realloc`函数进行内存重新分配实现内存的扩充；而 new 没有直观的内存扩充方式，需要手动实现；
8. new 可以调用 malloc， 而 `malloc` 不可以调用 `new`；
9. new 操作符从自由存储区（free store）上为对象动态分配内存空间，而malloc函数从堆上动态分配内存；
10. `new`是类型安全的，一个 `int`型的指针指向 `float`会报错；`malloc`不是类型安全的；( `malloc`类型转换为 `int`，分配 `double`数据类型大小的内存空间不会报错)
11. `delete`释放内存后会将指针值设置成 `nullptr`，以避免野指针，而 `free`不会修改指针的值，可能导致野指针的问题；


**malloc 或者 new 最多可以申请多大的内存空间？**
在C语言中，malloc函数可以申请的最大空间取决于系统的物理内存大小以及系统的内存管理方式。一般来说，在32位系统上，由于地址空间有限，malloc函数一次最多可以分配大约2GB的内存空间


**自由存储区和堆区的区别**

自由存储区是 C++ 基于new操作符的一个抽象概念，凡是通过 new 操作符进行内存申请，该内存即为自由存储区。那么自由存储区是否能够是堆（问题等价于new是否能在堆上动态分配内存），这取决于operator new 的实现细节。自由存储区**不仅可以是堆**，还可以是**静态存储区**，这都看operator new在哪里为对象分配内存。不过我们一般可以将自由存储区等价为堆。

自由存储区（或堆区）是用于动态分配内存的一块较大的、连续的内存区域。与栈区不同，自由存储区的生命周期不受函数的调用和返回限制，可以在程序的任意位置进行内存的分配和释放。主要区别如下：
- 分配方式：自由存储区使用new操作符来分配内存。
- 内存管理：自由存储区的内存分配和释放需要手动控制，开发者需要负责在适当的时候使用delete操作符释放已分配的内存。而堆区的内存管理也是手动进行的，通常使用malloc()和free()函数来分配和释放内存。
- 分配粒度：自由存储区的内存分配粒度更大，可以分配任意大小的内存块，而堆区的内存分配粒度较小，一般以字节为单位。

### C和C++的区别？
1. C 是面向过程的编程，特点是函数；C++ 是面向对象的编程，特点是类。C++的三大特性是封装、继承、多态；
2. C 主要用在嵌入式开发、驱动开发和硬件直接打交道的领域；C++ 可以用于应用层的开发、用户界面开发等和操作系统直接打交道的领域。（应用领域）；
3. C++ 继承了C的底层操作特性，增加了面向对象的机制，增加了泛型编程、异常处理、运算符重载，还增加了命名空间，避免了命名冲突。（相较于 C 的升级）；

### 深浅拷贝的区别？
- 深拷贝：拷贝指针和指针指向的内容，深拷贝完成后，两个指针指向不同的地址；
- 浅拷贝：只是堆指针的拷贝，不拷贝指针指向的内容，浅拷贝完成后两个指针指向同一个地址；

### 虚函数和纯虚函数的区别？
1. 虚函数和纯虚函数可以定义在同一个类中，含有纯虚函数的类被称为抽象类，而含有虚函数的类不能被称为抽象类；
2. 虚函数可以直接被使用，也可以被子类重载后以多态的形式调用，而纯虚函数必须在子类中实现该函数才可以使用，纯虚函数在基类中只有声名而没有定义；
3. 虚函数和纯虚函数都可以在子类中被重载，以多态的形式被调用，目的是提供一个统一的接口；
4. 虚函数和纯虚函数的定义中不能有static标识符，因为 static 修饰的函数在编译时候要求前期绑定，而虚函数却是动态绑定，而且被两者修饰的函数声明周期也不一样；
5. 虚函数必须实现，如果不实现，编译器将报错。虚函数是C++中用于实现多态的机制。核心理念就是通过基类访问派生类定义的接口。包含纯虚函数的类叫做抽象类（也称为接口类），抽象类不能实例化处对象。

### 重载、重写、同名隐匿的区别？
- 重载：两个或多个函数在同一作用域函数名相同、参数不同，重载不关心函数的返回类型；
- 重写：磊哥函数分别在基类和派生类中，函数名、参数、返回值类型都相同，基类中函数必须为虚函数；
- 同名隐匿：两个函数分别在基类和派生类的作用域中函数名相同，派生类的函数屏蔽了与其同名的基类函数，只要是同名函数，不管参数列表是否相同，基类函数都会被隐藏；

### 继承和多态的区别与联系？
- 区别：继承是子类使用父类的方法，多态是父类使用子类的方法；
- 继承就是子类继承父类的特征和行为，使得子类具有父类的各种属性和方法，继承是为了重用代码，有效实现代码重用，减少重复代码的出现；
- 多态就是相同的事物，调用其相同的方法，参数也相同时，但表现的行为却不同，多态是为了接口重用，增强接口的扩展性；

### struct 和 class 的区别？
- struct 的成员默认是公有的，而类的成员默认是私有的；
- C 中的 struct 不能不含成员函数，而C++中的 struct 可以；

### extern 和 static 的区别？
1. extern(外部变量)：属于变量声明，extern int num 就是告编译器，有一个 int 型的变量 num 定义在其他地方，如果有调用请去其他文件中查找定义；
2. static(静态变量)：只会在第一次访问时初始化一次，在函数调用结束后，该变量也不会被释放，保留保存的值，直到程序运行结束时系统自动释放；

### 声明和定义的区别？
1. 声明指的是在程序中声明一个变量、函数或类，以便在程序中使用它。声明只是告诉编译器，这个名字存在，但并没有分配内存或定义实现。在C和C++中，可以使用关键字 extern 来显式声明变量或函数；
2. 定义指的是实际分配内存并实现变量、函数或类。定义包含了声明，但声明并不包含定义。在定义一个变量或函数时，必须给出其类型、名称和初始值（对于变量）。对于类，定义包括成员变量和成员函数的实现；
3. 声明是告诉编译器名字的存在，而定义是为名字分配内存并实现其功能；

### strcpy 和 memcpy 的区别？
1. strcpy 和 memcpy 都是在C语言和C++语言中用于复制内存块的函数，但它们在使用和效率上有所不同；
2. strcpy 用于将一个以null结尾的字符串从源地址复制到目标地址。它会复制整个字符串，包括 null 终止符，直到遇到null为止。如果源字符串长度超过目标地址所分配的内存空间，则会导致内存越界和缓冲区溢出问题；
3. memcpy 用于将一段内存块从源地址复制到目标地址，可以复制任意长度的内存块，而不仅限于字符串。memcpy 不会关心内存块中是否有 null 终止符，而只是按照给定的长度复制内存块。因此，使用 memcpy 时需要确保目标地址有足够的内存空间，否则也会导致缓冲区溢出问题；

### const 和 define 的区别？
- const 生效于编译的阶段；define 生效于预处理阶段；
- const 定义的常量，在C语言中是存储在内存中、需要额外的内存空间的；define 定义的常量，运行时是直接的操作数，并不会存放在内存中；
- const 定义的常量是带类型的；define 定义的常量不带类型。define 定义的常量不利于类型检查；

**宏定义函数**

`#define ADD(x, y) (x + y)`

### 内联函数和宏函数的区别？
1. 宏定义不是函数，但是使用起来像函数。预处理器用复制宏代码的方式代替函数的调用，省去了函数压栈退栈过程，提高了效率；而内联函数本质上是一个函数，内联函数一般用于函数体的代码比较简单的函数，不能包含复杂的控制语句，while、switch，并且内联函数本身不能直接调用自身；
2. 宏函数是在预编译的时候把所有的宏名用宏体来替换，简单的说就是字符串替换 ；而内联函数则是在编译的时候进行代码插入，编译器会在每处调用内联函数的地方直接把内联函数的内容展开，这样可以省去函数的调用的开销，提高效率；
3. 宏定义是没有类型检查的，无论对还是错都是直接替换；而内联函数在编译的时候会进行类型的检查，内联函数满足函数的性质，比如有返回值、参数列表等；

### 野指针和悬空指针的区别？
1. 野指针：访问一个已删除或访问受限的内存区域的指针，野指针不能判断是否为NULL来避免。指针没有初始化，释放后没有置空，越界；
2. 悬空指针：一个指针的指向对象已被删除；

### 值传递、指针传递、引用传递的区别？
1. 值传递：形参是实参的拷贝，改变形参的值并不会影响外部实参的值。从被调用函数的角度来说，值传递是单向的（实参->形参），参数的值只能传入，不能传出。当函数内部需要修改参数，并且不希望这个改变影响调用者时，采用值传递；
2. 指针传递：形参为指向实参地址的指针，当对形参的指向操作时，就相当于对实参本身进行的操作；
3. 引用传递：形参相当于是实参的“别名”，对形参的操作其实就是对实参的操作，在引用传递过程中，被调函数的形式参数虽然也作为局部变量在栈中开辟了内存空间，但是这时存放的是由主调函数放进来的实参变量的地址。被调函数对形参的任何操作都被处理成间接寻址，即通过栈中存放的地址访问主调函数中的实参变量。正因为如此，被调函数对形参做的任何操作都影响了主调函数中的实参变量。

### ++i与i++的区别？
1. 复制顺序不同。++i和i++都是分两步完成的。++i 是先加后赋值；i++ 是先赋值后加。既：
```cpp
// i++
i = i;
i += 1;

// ++i
i += 1;
i = i;
```
2. 效率不同：比如i=2，b=i++就是说b=2,完成之后让i变成3，b=++i就是先让i++变成3，然后b=3，其中++i比i++效率要高些。一般来说在循环域里面，这两者并没有什么很大的区别，但是要注意其生存周期，以及i值在程序流中的变化；

### 常量指针和指针常量的区别？
- 常ᰁ指针是指针本身是常ᰁ，内容可变。
- 指针常ᰁ是指针所指向的内容是常ᰁ，指针本身可变。
- 在常ᰁ指针中， const 关键字位于 * 之前。
- 在指针常ᰁ中， const 关键字位于 * 之后。


### struct 和 union 的区别？
union 是联合体，struct 是结构体：
- 联合体和结构体都是由若干个数据类型不同的数据成员组成。使用时，联合体只有一个有效的成员；而结构体所有的成员都有效；
- 对联合体的不同成员赋值，将会对覆盖其他成员的值，而对于结构体的对不同成员赋值时，相互不影响；
- 联合体的大小为其内部所有变量的最大值，按照最大类型的倍数进行分配大小；结构体分配内存的大小遵循内存对齐原则；

**内存对齐**

内存对齐是指将数据结构中的每个成员按照一定的规则进行排列，使得每个成员的起始地址相对于该结构的起始地址偏移量为该成员大小的整数倍。这样做的目的是为了让处理器在读取数据时更加高效，因为处理器可以一次性读取多个连续地址上的数据，如果数据不对齐，处理器就需要多次读取，降低了读取速度。

```cpp
# pragma pack(oush, 1) // 设置字节对齐为1字节，取消自动对齐
struct test{
  char a;
  int b;
  short c;
}
# pragma pack(pop) // 恢复默认的字节对齐设置

sizeof(test); // 结果为7
```




## 基础知识 => 定义/概念

### C++的三大特性？
C++的三大特性既是封装、继承、多态嘛。

#### 封装
封装就是将数据的属性和这些数据的函数组合在一个类的过程。封装的主要目的是隐藏数据函数的内部实现，仅暴露一个必要的调用接口。通过封装，我们可以控制成员的访问级别，如public、private和protected，限制对类内部数据的直接访问，确保数据的完整性和安全性。

#### 继承
继承就是一个类(派生类)从另一个类(基类)那里获得其属性和方法的过程。继承允许我们创建具有共享代码的类层次结构，减少重复代码，提高代码的复用性和可维护性。

#### 多态
多态就是允许不同类的对象使用相同的接口名字，但具有不同实现的特性。C++中多态主要通过虚函数来实现。

### 类有哪几种构造方式？
默认构造函数(没有参数)，有参构造函数(有参数)，拷贝构造函数(形参是本类对象的引用)，转换构造函数(形参是其他类型的变量，且只有一个形参)。

### 什么是多重继承？
⼀个类可以从多个基类（⽗类）继承属性和⾏为。在C++等⽀持多重继承的语⾔中，⼀个派⽣类可以同时拥有多个基类。
多重继承可能引⼊⼀些问题，如菱形继承问题, ⽐如当⼀个类同时继承了两个拥有相同基类的类，⽽最终的派⽣类⼜同时继承了这两个类时， 可能导致⼆义性和代码设计上的复杂性。

为了解决这些问题，C++ 提供了虚继承, 通过在继承声明中使⽤ virtual 关键字，可以避免在派⽣类中⽣成多个基类的实例，从⽽解决了菱形继承带来的二义性。
```cpp
class Animal{};
class Mammal : virtual public Animal{};
class Dog : virtual public Animal{};
```


### 说一下你对虚函数、纯虚函数、抽象类的理解？
- 虚函数：被 virtual 关键字修饰的成员函数；
- 纯虚函数：纯虚函数在类中声明时，加上 = 0；

包含纯虚函数的类叫做 **抽象类（也称为接口类），抽象类不能实例化出对象。** 抽象类中只有接口，没有具体的实现方法。 派生类继承抽象类之后只有重写全部纯虚函数，派生类才能实例化为对象。纯虚函数规范了派生类必须重写，接口继承。抽象类对象不能作为函数的参数，不能创建对象，不能作为函数返回类型。可以声明抽象类指针，可以声明抽象类的引用。

**虚函数的实现机制**

虚函数通过虚函数表来实现。虚函数的地址保存在虚函数表中，在类的对象所在的内存空间中，保存了指向虚函数表的指针（称为“虚表指针”），通过虚表指针可以找到类对应的虚函数表。虚函数表解决了基类和派生类的继承问题和类中成员函数的覆盖问题，当用基类的指针来操作一个派生类的时候，这张虚函数表就指明了实际应该调用的函数；
- 虚函数表存放的内容：类的虚函数的地址；
- 虚函数表建立的时间：编译阶段，即程序的编译过程中会将虚函数的地址放在虚函数表中；
- 虚表指针保存的位置：虚表指针存放在对象的内存空间中最前面的位置，这是为了保证正确取到虚函数的偏移量

### 你对空类了解多少？
空类声明时编译器不会生成任何成员函数，只会生成1个字节的占位符；

空类定义时编译器会生成 6 个成员函数：缺省的构造函数、拷贝构造函数、析构函数、赋值运算符、两个取址运算符(其中一个是 const 版)；

### C++中类对象的初始化顺序？
C++中类对象的初始化顺序遵循三条规则：
1. 基类初始化顺序：

如果当前类继承了多个基类，他们将按照生命的顺序进行初始化，但是在有虚继承了一般继承的情况下，优先虚继承；

2. 成员变量初始化顺序：

类的成员变量按照它们在类定义中的声明顺序进行初始化。(成员变量的初始化顺序只与声明的顺序有关)

3. 执行构造函数：

在基类和成员变量初始化完成后，执行类的构造函数。
  
**注意：** 类析构函数的执行顺序和构造函数执行的顺序完全相反。

### 引用会占用内存空间吗？
在大多数编程语言中，引用不会占用内存空间，因为引用只是指向现有的内存空间的别名。在C++中，引用是指向变量的别名，与指针不同的是，引用不能为null或指向其他变量。当程序使用引用时，它们只是将变量名与内存地址关联起来，因此不会产生额外的内存分配。


### C++的内存分配？
在C++中，内存分为5个区，分别是：
- 堆区：一般由程序员自动分配，如果程序员没有释放，程序结束时由OS回收；
- 栈区：由编译器自动分配和释放，存放为运行函数分配的局部变量，函数参数，返回数据，返回地址等；
- 全局区(静态 static .bss段 .data段)：存放全局变量、静态变量、常量。结束后由系统释放；
- 常量区(文字常量区 .data段)：存放常量字符串，程序结束后由系统释放；
- 代码区(.text段)：存放函数体(类成员函数和全局区)的二进制代码，不允许修改，但可以执行；

### C++ 编译过程？
编译过程分为四个过程：编译（编译预处理、编译、优化），汇编，链接：
- 编译预处理：处理以 # 开头的指令；
- 编译、优化：将源码 .cpp 文件翻译成 .s 汇编代码；
- 汇编：将汇编代码 .s 翻译成机器指令 .o 文件；
- 链接：汇编程序生成的目标文件，即 .o 文件，并不会立即执行，因为可能会出现：.cpp 文件中的函数引用了另一个 .cpp 文件中定义的符号或者调用了某个库文件中的函数。那链接的目的就是将这些文件对应的目标文件连接成一个整体，从而生成可执行的程序 .exe 文件；

链接又有两种，分别是静态链接和动态链接：
- 静态链接
1. 代码从其所在的静态链接库中拷贝到最终的可执行程序中，在该程序被执行时，这些代码会被装入到该进程的虚拟地址空间中；
2. 静态链接是指将程序中所有需要的代码和库函数都打包成一个完整的可执行文件，这个可执行文件独立运行，不需要依赖其他的库文件。当程序执行时，操作系统会将整个可执行文件加载到内存中并执行，因此静态链接的程序在执行时比较快，但可执行文件会比较大；
- 动态链接
1. 码被放到动态链接库或共享对象的某个目标文件中，链接程序只是在最终的可执行程序中记录了共享对象的名字等一些信息。在程序执行时，动态链接库的全部内容会被映射到运行时相应进行的虚拟地址的空间；
2. 动态链接是指将程序中需要的库函数等文件打包成一个动态链接库文件（DLL、so等），程序在运行时通过动态链接库中的函数进行调用。因为多个程序可以共享同一个动态链接库，因此可以节省内存和磁盘空间。但由于需要在程序运行时进行动态链接，因此相比于静态链接，动态链接的程序在启动时会稍微慢一些；
3. 动态链接还可以实现库文件的动态更新和升级，因为不需要重新编译整个程序，只需要替换动态链接库即可；

### explicit 的作用？
用来声明类构造函数是显示调用的，而非隐式调用，可以阻止调用构造函数时进行隐式转换。只可用于修饰单参构造函数，因为无参构造函数和多参构造函数本身就是显示调用的，再加上 explicit 关键字也没有什么意义；

**什么是隐式类型转换？**

比如说你有一个 `int` 型的变量 a ，当你 执行代码 `long b = a + 1；`时，`int` 型的 a 就隐式变成 `long` 型了，这就是隐式类型转换。


### C++中 `const` 关键字？
在 C/C++ 中，`const` 是一个关键字，用于表示常量。`const` 可以用于修饰变量、函数、指针等，主要作用有以下几种：
1. 当 `const` 修饰变量时，该变量被视为只读变量，既不能被修改；
2. 当 `const` 修饰参数时，表示函数内部不会修改该参数的值，在引用传参时使用的比较多；
3. 当 `const` 修饰函数返回值时，表示函数的返回值为只读，不能被修改； 如`const int func(){}`
4. 当 `const` 修饰成员函数时，表示该函数不会修改成员的状态； 如`int func() const{}`
5. 当 `const` 修饰指针时，可以分为以下三种情况：

- 指向只读变量的指针：这种情况下，const 关键字修饰的是指针所指向的变量，而不是指针本身。指针本身可以被修改（意思是指针可以指向新的变量），但是不能通过指针修改所指向的变量。 `const int* p;`
- 只读指针：这种情况下，const 关键字修饰的是指针本身，使得指针本身成为只读变量。指针本身不能被修改（即指针一旦初始化就不能指向其它变量），但是可以通过指针修改所指向的变量。`int* const p = &a` , a的值可以改变，但p不能指向其他变量；
- 只读指针指向只读变量：这种情况下，const 关键字同时修饰了指针本身和指针所指向的变量，使得指针本身和所指向的变量都成为只读变量。指针本身不能被修改，也不能通过指针修改所指向的变量。 `const int* const p = &a;`


### C++中 `static` 关键字？
`static` 是C++中一个重要的关键字，它可以用于变量、函数和类中。
1. `static`修饰全局变量：`static`修饰全局变量可以将变量的作用域限定在当前文件中，使得其他文件无法访问该变量。 同时，static 修饰的全局变量在程序启动时被初始化（可以简单理解为在执行 main 函数之前，会执行一个全局的初始化函数，在那里会执行全局变量的初始化），生命周期和程序一样长。
2. `static`修饰局部变量：`static`修饰的局部变量使得变量在函数调用结束后一直在内存中，不被销毁，下次调用该函数时可以继续使用。且该局部变量仅在第一次调用函数时进行一次初始化。同时，该变量的作用域只在函数里，其他函数无法访问该变量；
3. `static` 修饰函数：`static` 修饰函数可以将该函数的作用域限定在该文件中，使得其他文件无法访该函数，因此也可以避免命名冲突和代码重复定义。
4. `static` 修饰类成员变量和函数：`static` 修饰的成员变量和函数可以使得它们在所有该类对象中共享，且不需要创建对镜就可以直接访问。

### 什么是内存泄漏？

由于疏忽或错误导致的程序未能释放已经不再使用的内存造成了内存的浪费。以发生的方式分类，内存泄漏可以分为4类：

1. 常发性内存泄漏：发生发生内存泄漏的代码被多次执行到，每次被执行的时候都会导致一块内存泄漏
2. 偶发性内存泄漏：发生内存泄漏的代码只在某些特定环境或操作过程下才会发生。
3. 一次性内存泄漏：发生内存泄漏的代码只会被执行一次，或者由于算法上的缺陷，导致总会有一块且仅一块内存发生泄漏。比如，在类的构造函数中分配内存，在析构函数中却没有释放该内存。
4. 隐式内存泄漏。程序在运行过程中不停的分配内存，但是直到结束时才释放内存。严格来说没有发生内存泄漏，但是不及时释放内存也可能导致最终耗尽系统的内存，所以称为隐式内存泄漏

### 内存泄漏产生的原因？

1. 堆内存泄漏，new 跟 delete 、malloc 和 free 未成对使用；
2. 系统资源泄漏， Bitmap, handle,socket 等资源未释放；
3. 没有将基类析构函数定义称为虚函数，（使用基类指针或者引用指向派生类对象时）派生类对象释放时将不能正确释放派生对象部分；
4. 指针重新赋值，指针重新赋值后原来指向的内存空间丢失，发生了内存泄漏；
   
### 如何避免内存泄漏？

1. 程序员手动检查 new 与 delete , malloc 与 free 有没有成对使用；
2. 使用内存泄漏检测工具，如Linux下的 `valgrind`，它会显示你在堆区 `alloc`了多少次空间，`free`了多少次空间；
3. 使用RAII机制，将指针在使用前与某个对象的声明周期绑定在一起；
4. 使用智能指针；

#### 什么是RAII机制

`RAII`既是资源获取既初始化（Resource Acquisition Is Initialization，简称 RAII），它是一种C++编程技术，在使用前获取的资源与某个对象的生命那个周期绑定在一起。确保在控制对象的生命周期结束时，按照资源获取的顺序来释放所有资源。

`RAII`的核心原理：**利用栈上局部变量的自动析构来保证资源一定会被释放**

#### `RAII`的实现步骤：
- 设置一个类封装资源，资源可以是内存、文件、socket和锁等；
- 在构造函数中执行资源的初始化。比如申请内存、打开文件、申请锁等；
- 在析构函数中执行销毁操作，比如释放内存、关闭文件、释放锁等；
- 使用时声明一个该对象的类，一般在你希望的作用域即可，比如在函数的开始，或者作为类的成员变量；

### 面向对象设计原则

1. **单一职责原则：** 一个类只负责一个功能领域中的相应职责。或者说，一个类，最好只做一件事，只有一个引起它变化的原因。
2. **开闭原则：** 一个软件实体应当对拓展开放，对修改关闭。即软件实体应尽量在不修改原有代码的情况下进行拓展。
3. **里氏替换原则：** 所有引用基类（父类）的地方必须能透明的使用其子类的对象，并且保证原来程序的逻辑行为不变以及正确性不被破坏。
4. **依赖倒置原则：** 抽象不应该依赖于细节，细节应当依赖于抽象。换句话说，要针对接口编程，而不是针对实现编程。
5. **接口隔离原则：** 使用多个专门的接口，而不使用单一的总接口，即客户端不应该依赖那些他不需要的接口。
6. **合成复用原则：** 尽量使用对象组合，而不是继承来达到复用的目的。

#### 工厂模式
定义一个创建对象的接口，其子类去具体现实这个接口以完成具体的创建工作。如果需要增加新的产品类，只需要扩展一个相应的工厂类即可。缺点：产品类数据较多时，需要实现大量的工厂类，这无疑增加了代码量。

#### 单例模式(Singleton)
单例模式顾名思义，保证一个类仅可以有一个实例化对象，并且提供一个可以访问它的全局接口。
- 单例类只能由一个实例化对象。
- 单例类必须自己提供一个实例化对象。
- 单例类必须提供一个可以访问唯一实例化对象的接口。

#### 懒汉模式
懒汉：故名思义，不到万不得已就不会去实例化类，也就是说在第一次用到类实例的时候才会去实例化一个对象。在访问量较小，甚至可能不会去访问的情况下，采用懒汉实现，这是以时间换空间。

#### 饿汉模式
饿汉：饿了肯定要饥不择食。所以在单例类定义的时候就进行实例化。在访问量比较大，或者可能访问的线程比较多时，采用饿汉实现，可以实现更好的性能。这是以空间换时间。

#### 代理模式
代理模式：为其它对象提供一种代理以控制这个对象的访问。在某些情况下，一个对象不适合或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介作用。优点：

- 职责清晰。真实的角色只负责实现实际的业务逻辑，不用关心其它非本职责的事务，通过后期的代理完成具体的任务。这样代码会简洁清晰。
- 代理对象可以在客户端和目标对象之间起到中介的作用，这样就保护了目标对象。
- 扩展性好。

#### 观察者模式
观察者模式：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都要得到通知并自动更新。

观察者模式从根本上讲必须包含两个角色：观察者和被观察对象。

- 被观察对象自身应该包含一个容器来存放观察者对象，当被观察者自身发生改变时通知容器内所有的观察者对象自动更新。
- 观察者对象可以注册到被观察者的中，完成注册后可以检测被观察者的变化，接收被观察者的通知。当然观察者也可以被注销掉，停止对被观察者的监控。

## STL库

### 各个常见容器

#### vector-向量容器
- 内部数据结构：一端开口的连续存储的数组形式
- 特点：增加或获取元素效率很高，插入和删除的效率很低
- 操作元素的方式：下标操作符[]
- 迭代器失效：插入和删除元素都会是迭代器失效

1. vector 底层原理？
- 数据安排及操作方式与数组非常相似。两者的唯一差别在于空间运用的灵活性；
- 静态空间，一旦配置好了就不能改变了，如果程序需要一个更大的array，只能自己再申请一个更大的array，然后将以前的array中的内容全部拷贝到新的array中；
- 动态开辟的空间，随着元素的加入，它的内部机制会自动扩充空间以容纳新的元素。vector的关键技术在于对大小的控制以及重新分配时的数据移动效率；
- 采用的数据结构是线性的连续空间（泛型的动态类型顺序表），他以两个迭代器start和finish分别指向配置得来的连续空间中目前已将被使用的空间。迭代器end_of_storage指向整个连续的尾部；
- 在增加元素时，如果超过自身最大的容量，vector则将自身的容量扩充为原来的两倍。扩充空间需要经过的步骤：重新配置空间，元素移动，释放旧的内存空间。一旦vector空间重新配置，则指向原来vector的所有迭代器都失效了，因为vector的地址改变了；

2. vector 的扩容过程？
- 完全弃用现有的内存空间中，重新申请更大的内存空间；
- 将旧的内存空间中的数据按原有顺序移动到新的内存空间中；
- 最后将旧的内存空间释放；

3. vector 的扩容方式为什么是1.5倍或2倍？
假如说我们是以 2 倍方式扩容（1，2，4，8，16），则第 i 次扩容期间所需要的空间总量就是 2<sup>i</sup>次方，如果第 4 次扩容时总共需要8个元素大小的空间，但是前3次已经释放的空间加起来的总量，刚好是7，而7小于8，不足以我们第4次扩容时所需要的空间，也就是说，如果恰巧以2倍方式扩容，那么每次扩容时前面释放的空间它都不足以支持本次的扩容！！！那么如果是以更高倍数的方式进行扩容，则这个空间它的浪费情况就会更高！！！因此会有以下两个问题：
- 空间浪费的可能比较大；
- 无法使用前面释放的内存空间；
一般情况下，在Windows的VS系列编译器下，是按照1.5倍的方式进行扩容，在Linux的g++中，是按照2倍的方式进行扩容的；

4. size、resize、reserve、capacity 的区别？
- size表示当前vector中有多少个元素（即finish – start）；
- resize可以改变有效空间的大小，也有改变默认值的功能。capacity的大小也会随着改变。可以有多个参数。创建指定数量的元素并指定vector的存储空间。既分配空间又创建对象；
- reserve是直接扩充到已经确定的大小，可以减少多次开辟、释放空间的问题（优化push_back），从而达到提高效率的目的，其次还可以减少多次要拷贝数据的问题。reserve它只是保证vector中的空间大小（capacity）最少达到参数所指定的大小n。并且它只有一个参数。指定vector的元素总数，不创建对象；
- capacity函数表示它已经分配的内存中可以容纳多少元素（即end_of_storage – start）。即容器在分配新的存储空间能存储的元素总数。返回vector中能存储元素的最大数；

5. push_back 和 emplace_back 的区别？
emplace_back() 和 push_back() 的主要区别，就在于底层实现的机制不同。push_back() 向容器尾部添加元素时，首先会创建这个元素，然后再将这个元素拷贝或者移动到容器中（如果是拷贝的话，事后会自行销毁先前创建的这个元素）；而 emplace_back() 在实现时，则是直接在容器尾部创建这个元素，省去了拷贝或移动元素的过程；


#### list-列表容器
- 内部数据结构：双向循环链表
- 特点：增加或获取元素效率很低，插入和删除效率很高
- 操作元素的方式：只能用迭代器
- 迭代器失效：插入，迭代器不会失效。删除，指向被删除节点的迭代器失效

常用函数
```cpp
list.push_back(elem)    在尾部加入一个数据

list.pop_back()         删除尾部数据

list.push_front(elem)   在头部插入一个数据

list.pop_front()        删除头部数据

list.size()             返回容器中实际数据的个数

list.sort()             排序，默认由小到大 

list.unique()           移除数值相同的连续元素

list.back()             取尾部迭代器

list.erase(iterator)    删除一个元素，参数是迭代器，返回的是删除迭代器的下一个位置
```


#### deque-双向队列容器
- 内部数据结构：两端开口的连续或分段存储的数组
- 特点：增加或获取元素效率较高，插入和删除效率较高
- 操作元素的方式：下标操作符或迭代器
- 迭代器失效：插入任何元素都会使迭代器失效。删除头和尾元素，指向被删除节点迭代器失效，而删除中间元素会使所有迭代器失效

deque内部实现的是一个双向队列。元素在内存连续存放。随机存取任何元素都在常数时间完成（仅次于vector）。所有适用于vector的操作都适用于deque。在两端增删元素具有较佳的性能（大部分情况下是常数时间）。

1. 你了解 deque 的中控器吗？
deque为了维持整体连续的假象，设计一个中控器，其用来记录deque内部每一段连续空间的地址。大体上可以理解为deque中的每一段连续空间分布在内存的不连续空间上，然后用一个所谓的map作为主控，记录每一段内存空间的入口，从而做到整体连续的假象；

2. deque 的迭代器是怎么回事？
- deque提供的是一个随机访问迭代器，由于是分段连续空间，其必须记录当前元素所在段的信息，从而在该段连续空间的边缘进行前进或者后退的时候能知道跳跃到的上一个或下一个缓冲区。deque必须完完整整的掌握和控制这些信息，以达到正确的跳跃；

```cpp
static size_t buffer_size(){
    return __deque_buf_size(BufSiz, sizeof(T));
}
//如果n不为0,传回n,表示buffer size 由自己定义
如果n为0,表示buffer_size 采用默认值
如果sz(元素大小) < 512，传回512/sz,如果不小于512 ，传回1
inline size_t __deque_buf_size(size_t n, size_t sz)
{
    return n != 0 ? n : (sz < 512 ? size_t(512 / sz) : size_t(1));
}
```

- 当迭代器处在当前缓冲区的边缘时，一旦前进或者后退，就要考虑超过当前缓冲区的情况，此时需要跳转到下一个缓冲区，这时候需要用到set_node函数：

```cpp
void set_node(map_pointer new_node)
{
  node = new_node;      // 跳转到相应缓冲区
  first = *new_node;    // 更新跳转后缓冲区first信息
  last = first + difference_type(buffer_size());  // 更新跳转后缓冲区last的信息
}
```

3. 说一说 deque 的数据结构?
deque维护着一个map，用来记录每个缓冲区的位置。除了map外，deque的数据结构还维护着start和finish两个迭代器，分别指向deque的首尾。此外，他还必须知道map的大小，一旦map提供的节点不足，就需要配置一块更大的map;

常用函数
```cpp
deque.push_back(elem)在尾部加入一个数据。

deque.pop_back()删除尾部数据。

deque.push_front(elem)在头部插入一个数据。

deque.pop_front()删除头部数据。

deque.size() 返回容器中实际数据的个数。

deque.at(idx)传回索引idx所指的数据，如果idx越界，抛出out_of_range。
```


#### map-映射
- 内部数据结构：红黑树
- 特点：键和值分开，前面是键后面是值,键唯一，元素默认按键的升序排列
- 操作元素的方式：迭代器
- 迭代器失效：插入，迭代器不会失效。删除，指向被删除节点的迭代器失效

1. map 和 unordered_map 的底层实现？
- map
map内部实现了一个红黑树（红黑树是非严格平衡的二叉搜索树，而AVL是严格平衡二叉搜索树），红黑树有自动排序的功能，因此map内部所有元素都是有序的，红黑树的每一个节点都代表着map的一个元素。因此，对于map进行的查找、删除、添加等一系列的操作都相当于是对红黑树进行的操作。map中的元素是按照二叉树（又名二叉查找树、二叉排序树）存储的，特点就是左子树上所有节点的键值都小于根节点的键值，右子树所有节点的键值都大于根节点的键值。使用中序遍历可将键值按照从小到大遍历出来；

- unordered_map
unordered_map内部实现了一个哈希表（也叫散列表），通过把关键码值映射到Hash表中一个位置来访问记录，查找时间复杂度可达O（1），其中在海量数据处理中有着广泛应用。因此，元素的排列顺序是无序的；


#### set-集合
- 内部数据结构：红黑树
- 特点：键和值相等，键唯一，元素默认按升序排列
- 操作元素的方式：迭代器
- 迭代器失效：插入，迭代器不会失效。删除，指向被删除节点的迭代器失效



### 常见面试问题

#### 讲讲STL的六大组件？
1. 容器（Containers）：各种数据结构，如 Vector,List,Deque,Set,Map,用来存放数据，STL容器是一种 Class Template ；

2. 算法（Algorithms）：各种常用算法如Sort,Search,Copy,Erase,从实现的角度来看，STL算法是一种Function Templates ；

3. 迭代器（Iterators）：扮演容器与算法之间的胶合剂，是所谓的“泛型指针”，共有五种类型，以及其它衍生变化，从实现的角度来看，迭代器是一种将：Operators*,Operator->,Operator++,Operator--等相关操作予以重载的 Class Template。所有STL容器都附带有自己专属的迭代器；

4. 仿函数（Functors）：行为类似函数，可作为算法的某种策略（Policy）,从实现的角度来看，仿函数是一种重载了Operator()的Class 或 Class Template ；

5. 适配器（配接器）（Adapters）：一种用来修饰容器（Containers）或仿函数（Functors）或迭代器（Iterators）接口的东西，例如：STL提供的 Queue 和 Stack ，虽然看似容器，其实只能算是一种容器配接器，因为 它们的底部完全借助 Deque，所有操作有底层的 Deque 供应。改变 Functor 接口者，称为 Function Adapter ;改变 Container 接口者，称为 Container Adapter;改变 Iterator 接口者，称为Iterator Adapter；

6. 配器（Allocators）：负责空间配置与管理，从实现的角度来看，配置器是一个实现了动态空间配置、空间管理、空间释放的Class Template ；


#### 说说优先级队列？
优先队列底层数据结构一般以 vector 为底层容器，heap 为处理规则来管理底层容器实现。优先队列（priority_queue）容器与队列一样，**只能从队尾插入元素，从队首删除元素。** 但是它有一个特性，**队列中最大的元素总是位于队首。** 出队时，并非按照先进先出的原则进行，而是将当前队列中最大的元素出队。这点类似于给队列里的元素进行了由大到小的顺序排序。元素的比较规则默认按元素值由大到小排序，可以重载“<”操作符来重新定义比较规则。**在优先队列中，队首元素一定是当前队列中优先级最高的那一个；**

#### map和unordered_map的优缺点？
- map优点：有序、基于红黑树实现，查找的时间复杂度为O(nlogn)；
- map缺点：空间占用率比较高，虽然底层是红黑树实现的，提高了运行效率，但是每个节点都要保存父节点和孩子节点和红黑树的性质，使得每一个节点都占用胆量的空间；
- 适用场景：对于有序的结构，小规模的数据量；

- nordered_map优点：底层是用哈希表实现的，查找效率非常高，时间复杂度为O(1)；
- unordered_map的缺点：哈希表的建立比较费时；
- 适用场景：对于查找问题，使用unordered_map更好，大规模的数据量；

#### 为什么map和set和插入删除效率比其他序列容器高，而且每次insert之后，以前保存的iterator不会失效？
因为存储的是节点，不需要内存拷贝和内存移动。插入操作只是节点指针换来换去，节点内存没有改变，而iterator就像指向节点的指针，内存没变，指向内存de指针也不会变。

#### 为什么 map 和 set 不能像 vector 一样有个 reserve 函数来预分配数据？
因为在map和set内部存储的已经不是元素本身了，而是包含元素的节点。

#### map 中[ ]与 find 的区别？
- map的下标运算符[ ]的作用是：将关键码作为下标去执行查找，并返回对应的值；如果不存在这个关键码，就将一个具有该关键码和值类型的默认值的项插入这个map；
- map的find函数：用关键码执行查找，找到了返回该位置的迭代器；如果不存在这个关键码，就返回尾迭代器 end ；

#### hash_map与map的区别？什么时候用hash_map，什么时候用map？
- 构造函数：hash_map需要hash function和等于函数，而map需要比较函数（大于或小于）；
- 存储结构：hash_map以hashtable为底层，而map以RB-TREE为底层；
- 总的说来，hash_map 查找速度比 map 快，而且查找速度基本和数据量大小无关，属于常数级别。而 map 的查找速度是 logn 级别。但不一定常数就比 logn 小，而且hash_map 还有 hash function 耗时。如果考虑效率，特别当元素达到一定数量级时，用 hash_map ；考虑内存，或者元素数量较少时，用 map 。

#### 讲一讲set的用法和它的特点？
- 用法：count()-返回某个值元素的个数（set中最多为1）、find()-返回一个指向被查找到元素的迭代器、equal_range()-返回集合中与给定值相等的上下限的两个迭代器；
- 特点：元素不允许有重复，在默认情况下会对元素进行自动排序，数据被组织成一棵红黑树，查找的速度非常快（二分），时间复杂度是O(logn)，set中的元素不能被修改，只能删除后再添加；



## 十大排序算法

### 冒泡排序
从前往后（或从后往前）两两比较相邻元素的值，若为逆序（即A[I-1]>A[I]）,则交换它们，直到序列比较完。我们称它为第一趟冒泡，结果是将最小的元素交换到待排序列的第一个位置（或将最大的元素交换到待排序列的最后一个位置），关键字最小的元素如气泡一样逐渐向上“漂浮”。最终一个一个排好了位置；

```cpp
void bubbleSort(vector<int>& nums) {
    int n = nums.size();
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (nums[j] > nums[j+1]) {
                swap(nums[j], nums[j+1]);
            }
        }
    }
}
```
- 时间复杂度：O(N<sup>2</sup>)；

### 快速排序
通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据比另一部分的所有数据要小，再按这种方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，使整个数据变成有序序列。每一趟中，都至少可以确定一个元素的最终位置。

```cpp
// 快速排序 注：开始时right = 数组的最大有效下标
void QuickSort(int *arr, int left, int right){
  if(left >= right)
    return;
  int key = arr[left];
  int low = left;
  int high = right;
  while(low < high){
    while(low < high && arr[high] >= key)
      high--;
    arr[low] = arr[high];
    while(low < high && arr[low] <= key)
      low++;
    arr[high] = arr[low];
  }
  arr[low] = key;
  QuickSort(arr, left, low - 1);
  QuickSort(arr, low + 1, right);
}
```

**快速排序的优化**

1. 随机选择基准元素
快速排序最坏情况下的时间复杂度为 O(N<sup>2</sup>)，这种情况通常发生在每次选择的基准元素都是当前子数组的最大或最小值时。为了避免这种情况，我们可以随机选择一个元素作为基准元素，这样每个元素都有相同的概率成为基准元素，从而避免了最坏情况的发生；

2. 三数取中法选择基准元素
在确定基准元素时，我们可以选择当前子数组的第一个元素、最后一个元素、中间元素中的中位数作为基准元素。这种方式称为三数取中法，可以使得基准元素更加均衡，从而提高排序效率；

3. 双轴快排
双轴快排是一种基于快速排序的改进算法，它使用两个基准元素而不是一个基准元素进行分区。具体来说，我们先选择两个基准元素p和q，其中p < q，然后将数组分成三部分：小于p的部分、大于q的部分和介于p和q之间的部分。接下来，我们对小于p和大于q的两部分递归进行双轴快排，对介于p和 q之间的部分进行普通的快速排序。双轴快排相比于普通的快速排序，在某些情况下可以提高排序效率；

### 选择排序
每次从待排序序列中选择最小的元素，与序列的第一个元素交换位置。这样，序列的第一个位置就是最小的元素。然后在剩下的元素中继续执行上述操作，直到整个序列排序完成。

```cpp
void selectionSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        int temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
}
```

- 时间复杂度：O(N<sup>2</sup>)；


### 插入排序
它的基本思想是将一个记录插入到已经排好序的有序表中，从而一个新的、记录数增1的有序表。在其实现过程使用双层循环，外层循环对除了第一个元素之外的所有元素，内层循环对当前元素前面有序表进行待插入位置查找，并进行移动;

插入排序的工作方式像许多人排序一手扑克牌。开始时，我们的左手为空并且桌子上的牌面向下。然后，我们每次从桌子上拿走一张牌并将它插入左手中正确的位置。为了找到一张牌的正确位置，我们从右到左将它与已在手中的每张牌进行比较。拿在左手上的牌总是排序好的，原来这些牌是桌子上牌堆中顶部的牌;

```cpp
void insertionSort(int arr[], int n) {
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

```

- 时间复杂度：O(N<sup>2</sup>)；


### 希尔排序
希尔排序的基本思想是将待排序的元素分成若干个小组，对每个小组进行插入排序，随着排序过程的进行，每个小组的元素个数逐渐增多，但仍然保持有序。最后将所有元素分成一个组，进行插入排序。希尔排序的具体步骤如下：
1. 选择一个增量序列d1，d2，...，dk，其中di > dj，dk = 1；
2. 对于每个增量di，将序列分成di个子序列，分别对每个子序列进行插入排序；增量逐渐缩小，重复步骤2，直到增量为1。
在实际应用中，希尔排序常常使用一些常见的增量序列，如希尔增量（n/2，n/4，...，1）、Hibbard增量（1，3，7，...，2^k-1）、Sedgewick增量等，以提高排序的效率；

```cpp
void shellSort(int arr[], int n) {
    for (int gap = n / 2; gap > 0; gap /= 2) {
        for (int i = gap; i < n; i++) {
            int temp = arr[i];
            int j;
            for (j = i; j >= gap && arr[j - gap] > temp; j -= gap) {
                arr[j] = arr[j - gap];
            }
            arr[j] = temp;
        }
    }
}

```
- 时间复杂度：O(N<sup>2</sup>)；


### 归并排序
归并排序是一种基于分治思想的排序算法，它的基本思想是将待排序的序列不断地二分为两个子序列，直到每个子序列只剩下一个元素。然后，将两个子序列归并成一个有序序列，不断地归并，直到最终得到一个有序序列；

```cpp
void merge(int arr[], int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    int leftArr[n1], rightArr[n2];
    for (int i = 0; i < n1; i++) {
        leftArr[i] = arr[left + i];
    }
    for (int j = 0; j < n2; j++) {
        rightArr[j] = arr[mid + 1 + j];
    }
    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (leftArr[i] <= rightArr[j]) {
            arr[k] = leftArr[i];
            i++;
        } else {
            arr[k] = rightArr[j];
            j++;
        }
        k++;
    }
    while (i < n1) {
        arr[k] = leftArr[i];
        i++;
        k++;
    }
    while (j < n2) {
        arr[k] = rightArr[j];
        j++;
        k++;
    }
}

void mergeSort(int arr[], int left, int right) {
    if (left < right) {
        int mid = (left + right) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

```

- 时间复杂度：O(NlogN)；


### 堆排序
堆排序是一种基于堆数据结构的排序算法，其基本思想是将待排序的元素构造成一个堆，然后依次将堆顶元素与堆底元素交换，再对堆顶元素进行下沉操作，使得交换后的堆仍然保持最大堆或最小堆的性质，重复上述过程直到排序完成。

在堆排序中，首先要构建一个堆，可以使用从下往上的建堆方法，或者使用堆插入的方法。建堆完成后，将堆顶元素与堆底元素交换，然后对堆顶元素进行下沉操作，使得堆顶元素重新满足最大堆或最小堆的性质。交换后的堆除堆顶元素外，仍然满足最大堆或最小堆的性质，继续进行相同的操作，直到排序完成。

堆排序的时间复杂度为O(NlogN)，空间复杂度为O(1)。堆排序是一种不稳定的排序算法，因为交换操作会改变相同元素之间的相对位置；

```cpp
// 堆排序(大根堆) 注：length = 数组的最大有效下标
void HeapAdjust(int *arr, int length, int aj_idx){
  arr[0] = arr[aj_idx];
  int i = 0;
  for(i = aj_idx * 2;i <= length; i *= 2){
    if(i < length && arr[i] < arr[i + 1])
      i++;
    if(arr[0] < arr[i]){
      arr[aj_idx] = arr[i];
      aj_idx = i; 
    }
    else
      break;
  }
  arr[aj_idx] = arr[0];
}

void BigHeapBuild(int *arr, int length){
  for(int i = (length / 2) + 1; i > 0; i--)
      HeapAdjust(arr, length, i);
}
  
void BigHeapSort(int *arr, int length){
  // 调整成大根堆
  BigHeapBuild(arr, length);
  // 堆排序
  for(int i = length; i > 1; i--){
    arr[0] = arr[i];
    arr[i] = arr[1];
    arr[1] = arr[0];
    HeapAdjust(arr, i - 1, 1);
  }
}
```


### 计数排序
计数排序的基本思想是对于给定的输入序列中的每一个元素x，确定该序列中值小于x的元素的个数（此处并非比较各元素的大小，而是通过对元素值的计数和计数值的累加来确定）。一旦有了这个信息，就可以将x直接存放到最终的输出序列的正确位置上。例如，如果输入序列中只有17个元素的值小于x的值，则x可以直接存放在输出序列的第18个位置上;

```cpp
void countingSort(int arr[], int n, int k) {
    int count[k + 1] = {0};
    int output[n];
    for (int i = 0; i < n; i++) {
        count[arr[i]]++;
    }
    for (int i = 1; i <= k; i++) {
        count[i] += count[i - 1];
    }
    for (int i = n - 1; i >= 0; i--) {
        output[count[arr[i]] - 1] = arr[i];
        count[arr[i]]--;
    }
    for (int i = 0; i < n; i++) {
        arr[i] = output[i];
    }
}

```
- 时间复杂度：O(N);
- 空间复杂度：O(N);


### 桶排序
桶排序（Bucket Sort）是一种线性时间复杂度的排序算法，它的基本思想是将待排序的元素分配到不同的桶中，每个桶内部进行排序，最后按照桶的顺序依次输出所有元素。桶排序的基本思想如下：

1. 确定桶的数量：根据待排序元素的特点，确定需要的桶的数量。
2. 将元素分配到桶中：遍历待排序的元素，根据某种映射关系将元素分配到对应的桶中。
3. 桶内排序：对每个非空桶内的元素进行排序，可以使用其他排序算法（如插入排序、快速排序等）或递归地使用桶排序。
4. 合并桶中元素：按照桶的顺序依次输出所有元素，即可得到有序序列。

```cpp
void bucketSort(float arr[], int n) {
    vector<float> bucket[n];
    for (int i = 0; i < n; i++) {
        int index = n * arr[i];
        bucket[index].push_back(arr[i]);
    }
    for (int i = 0; i < n; i++) {
        sort(bucket[i].begin(), bucket[i].end());
    }
    int index = 0;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < bucket[i].size(); j++) {
            arr[index++] = bucket[i][j];
        }
    }
}
```

### 基数排序
基数排序是一种非比较排序算法，它的基本思想是将待排序的元素分别按照位数的大小进行排序。一般的实现方法是先按照个位数排序，然后按照十位数排序，接着按照百位数排序，直到最高位数排完后，排序完成。具体的实现步骤如下：

1. 找出待排序数组中最大的数，确定最大数的位数，作为排序的轮数；
2. 对于每一位数，用计数排序或桶排序进行排序；
3. 将排序后的数组按照位数依次组合起来，得到最终结果。

基数排序的时间复杂度为O(NK)，其中K为最大数的位数，N为数组元素个数。当k比较小的时候，基数排序的效率较高。但是当k比较大时，需要分配较大的桶或计数器，空间复杂度会变高。

```cpp
void countingSort(int arr[], int n, int exp) {
    int count[10] = {0};
    int output[n];
    for (int i = 0; i < n; i++) {
        count[(arr[i] / exp) % 10]++;
    }
    for (int i = 1; i < 10; i++) {
        count[i] += count[i - 1];
    }
    for (int i = n - 1; i >= 0; i--) {
        output[count[(arr[i] / exp) % 10] - 1] = arr[i];
        count[(arr[i] / exp) % 10]--;
    }
    for (int i = 0; i < n; i++) {
        arr[i] = output[i];
    }
}

void radixSort(int arr[], int n) {
    int maxVal = arr[0];
    for (int i = 1; i < n; i++) {
        if (arr[i] > maxVal) {
            maxVal = arr[i];
        }
    }
    for (int exp = 1; maxVal / exp > 0; exp *= 10) {
        countingSort(arr, n, exp);
    }
}
```
