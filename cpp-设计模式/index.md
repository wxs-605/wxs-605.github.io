# CPP 设计模式


### 面向对象设计原则

1. **单一职责原则：** 一个类只负责一个功能领域中的相应职责。或者说，一个类，最好只做一件事，只有一个引起它变化的原因
   
2. **开闭原则：** 一个软件实体应当对拓展开放，对修改关闭。即软件实体应尽量在不修改原有代码的情况下进行拓展
   
3. **里氏替换原则：** 所有引用基类（父类）的地方必须能透明的使用其子类的对象，并且保证原来程序的逻辑行为不变以及正确性不被破坏
   
4. **依赖倒置原则：** 抽象不应该依赖于细节，细节应当依赖于抽象。换句话说，要针对接口编程，而不是针对实现编程
   
5. **接口隔离原则：** 使用多个专门的接口，而不使用单一的总接口，即客户端不应该依赖那些他不需要的接口
   
6. **合成复用原则：** 尽量使用对象组合，而不是继承来达到复用的目的

---

参考连接   [知乎-设计模](https://zhuanlan.zhihu.com/p/431714886)

---

### 工厂模式

工厂模式属于创建型模式，大致可以分成三类，分别是简单工厂模式、工厂方法模式和抽象工厂模式。

#### 简单工厂模式

需要在工厂类中做判断，从而创造相应的产品。当增加新的产品时，就需要修改工厂类；

**缺点：** 增加新的核类型时，就需要修改工厂类，违反了开放封闭原则：软件实体（类、模块、函数）可以扩展，但是不可修改；

#### 工厂方法模式

定义一个用于创建对象的接口，让子类决定实例化哪一个类；

**缺点：** 每增加一种产品，就需要增加一个对象的工厂；

#### 抽象工厂模式

提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类；

---

### 策略模式

策略模式是指定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。本模式使得算法可独立于使用它的客户而变化。也就是说这些算法所完成的功能一样，对外的接口一样，只是各自实现上存在差异。用策略模式来封装算法，效果比较好；

通俗的说，就是一个接口，多个实现方法；

### 适配器模式

适配器模式将一个类的接口转换成客户希望的另外一个接口，使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。它包括类适配器和对象适配器；

### **单例模式**

顾名思义，单例模式就是保证一个类仅可以有一个实例化对象，并且提供一个可以访问它的全局接口；

#### 懒汉模式

懒汉模式就是在使用到这个对象时才会实例化这个对象；

```cpp
#include <iostream>
using namespace std;

class LazySingleton {
private:
    static LazySingleton* instance;
    LazySingleton() {}  // 私有构造函数，防止外部实例化

public:
    static LazySingleton* getInstance() {
        if (!instance) {
            instance = new LazySingleton();
        }
        return instance;
    }

    void showMessage() {
        std::cout << "Lazy Singleton Pattern" << std::endl;
    }
};

LazySingleton* LazySingleton::instance = nullptr;

int main() {
    LazySingleton* lazyInstance1 = LazySingleton::getInstance();
    LazySingleton* lazyInstance2 = LazySingleton::getInstance();

    // 两个实例应该相等
    if (lazyInstance1 == lazyInstance2) {
        std::cout << "Same instance of Lazy Singleton" << std::endl;
    } else {
        std::cout << "Different instances of Lazy Singleton" << std::endl;
    }

    lazyInstance1->showMessage();

    return 0;
}
```

#### 饿汉模式

饿汉模式就是类在加载的时候就创建实例；

```cpp
#include <iostream>
using namespace std;

class EagerSingleton {
private:
    static EagerSingleton* instance;
    EagerSingleton() {}  // 私有构造函数，防止外部实例化

public:
    static EagerSingleton* getInstance() {
        return instance;
    }

    void showMessage() {
        std::cout << "Eager Singleton Pattern" << std::endl;
    }
};

EagerSingleton* EagerSingleton::instance = new EagerSingleton();

int main() {
    EagerSingleton* eagerInstance1 = EagerSingleton::getInstance();
    EagerSingleton* eagerInstance2 = EagerSingleton::getInstance();

    // 两个实例应该相等
    if (eagerInstance1 == eagerInstance2) {
        std::cout << "Same instance of Eager Singleton" << std::endl;
    } else {
        std::cout << "Different instances of Eager Singleton" << std::endl;
    }

    eagerInstance1->showMessage();

    return 0;
}
```

### 原型模式

原型模式就是用一份模板对象，拷贝出多个对象，原型模式的实现精髓是实现深拷贝函，既是拷贝构造函数；

### 外观模式

外观模式是当一个系统很复杂时，系统提供给客户的是一个简单的对外接口，而把里面复杂的结构都封装了起来。客户只需使用这些简单接口就能使用这个系统，而不需要关注内部复杂的结构；

举个例子，比如我们将一个cpp程序生成为一个可执行程序并执行，需要经历 预处理、编译、汇编和连接的复杂过程，但是在vs中，我们点击 `Run` 就可把这个程序跑起来，无需知道上面这些步骤的详细执行过程；

### 代理模式

为其他对象提供一种代理以控制对这个对象的访问。有四种常用的情况：（1）远程代理，（2）虚代理，（3）保护代理，（4）智能引用


