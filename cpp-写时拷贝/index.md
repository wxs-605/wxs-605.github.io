# CPP 写时拷贝


自己手写一个string类，实现写时拷贝，这里记录下实现要点；

默认构造函数、拷贝构造函数、赋值运算符重载、析构函数 ===>>> 引入计数机制，类似于 `shared_ptr`；

**写时拷贝实现策略：**

先判断引用计数是否为1，如果为1，则可直接修改；

如果不为1，计数减1，然后深拷贝数据，为计数器新开一个空间并将其设为1；

---

**什么时候发生写时拷贝？**

涉及修改数据的操作时发生写时拷贝，如*和 [] 操作，因此需要重载解引用运算符和 [] 运算符；

---

默认构造函数
```cpp
private:
    char* data;
    size_t* refCount;

    // Helper function to perform deep copy
    void deepCopy(const char* str) {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
        refCount = new size_t(1);
    }

public:
    String(const char* str = "") {
        deepCopy(str);
    }
```

拷贝构造函数
```cpp
 // Copy constructor
    String(const String& other) : data(other.data), refCount(other.refCount) {
        (*refCount)++;
    }
```

赋值运算符
```cpp
// Assignment operator
    String& operator=(const String& other) {
        if (this != &other) {
            (*refCount)--;
            if (*refCount == 0) {
                delete[] data;
                delete refCount;
            }
            data = other.data; 
            refCount = other.refCount;
            (*refCount)++;
        }
        return *this;
    }
```

析构函数
```cpp
// Destructor
    ~String() {
        (*refCount)--;
        if (*refCount == 0) {
            delete[] data;
            delete refCount;
        }
    }
```

重载解引用
```cpp
 // 重载解引用运算符
    char& operator*() {
        if((*refCount) == 1)
            return *data;
        (*refCount)--;
        deepCopy(data);
    }
```

重载[]运算符
```cpp
 // 重载[]运算符
    char& operator[](int index){
        if((*refCount) == 1)
            return data[index];
        (*refCount)--;
        deepCopy(data);
        return data[index];
    }
```

整体代码
```cpp
#include <iostream>
#include <cstring>

class String {
private:
    char* data;
    size_t* refCount;

    // Helper function to perform deep copy
    void deepCopy(const char* str) {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
        refCount = new size_t(1);
    }

public:
    String(const char* str = "") {
        deepCopy(str);
    }

    // Copy constructor
    String(const String& other) : data(other.data), refCount(other.refCount) {
        (*refCount)++;
    }

    // Destructor
    ~String() {
        (*refCount)--;
        if (*refCount == 0) {
            delete[] data;
            delete refCount;
        }
    }

    // Assignment operator
    String& operator=(const String& other) {
        if (this != &other) {
            (*refCount)--;
            if (*refCount == 0) {
                delete[] data;
                delete refCount;
            }
            data = other.data; 
            refCount = other.refCount;
            (*refCount)++;
        }
        return *this;
    }

    // Accessor method
    const char* c_str() const {
        return data;
    }

    // Mutator method to modify data
    void modify(const char* str) {
        if (*refCount > 1) {
            (*refCount)--;
            deepCopy(str);
        } else {
            delete[] data;
            strcpy(data, str);
        }
    }

    // 重载解引用运算符
    char& operator*() {
        if((*refCount) == 1)
            return *data;
        (*refCount)--;
        deepCopy(data);
    }

    // 重载[]运算符
    char& operator[](int index){
        if((*refCount) == 1)
            return data[index];
        (*refCount)--;
        deepCopy(data);
        return data[index];
    }
};

int main() {
    String s1("Hello");
    String s2 = s1; // Copy constructor, shared data
    String s3 = s2; // Copy constructor, shared data

    std::cout << "s1: " << s1.c_str() << std::endl;
    std::cout << "s2: " << s2.c_str() << std::endl;
    std::cout << "s3: " << s3.c_str() << std::endl;

    //s1.modify("World"); // Modify s1's data
    s1[2] = 'A';

    std::cout << "After modifying s1:" << std::endl;
    std::cout << "s1: " << s1.c_str() << std::endl;
    std::cout << "s2: " << s2.c_str() << std::endl;
    std::cout << "s3: " << s3.c_str() << std::endl;

    return 0;
}
```
