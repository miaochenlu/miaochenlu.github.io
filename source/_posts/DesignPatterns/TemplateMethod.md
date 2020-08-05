---
title: Template Method
date: 2020-08-05 16:10:25
index_img: /img/image-20200710192039991.png
tags: Design Patterns
---

## 动机

在软件构建过程中，对于某一项任务，它常常有稳定的整体操作结构，但各个子步骤却有很多改变的需求，或者由于固有的原因 （比如框架与应用之间的关系）而无法和任务的整体结构同时实现。

核心在于在稳定与变化之间求得一个平衡，如果全部稳定或者全部变化都是不需要设计模式的。

如何在确定**稳定操作结构**的前提下，来灵活应对**各个子步骤的变化**或者**晚期实现需求**？

假设整个代码的操作结构如下图所示，其中红色是Library开发人员完成的，蓝色部分是Application开发人员完成的

<img src="image-20200710191024075.png" alt="image-20200710191024075" style="zoom:33%;" />



## 结构化软件流程设计

<img src="image-20200710191212499.png" alt="image-20200710191212499" style="zoom:50%;" />

lib.cpp

```cpp
class Library {
public:
    void Step1() {
        //...
    }
    void Step3() {
        //...
    }
    void Step5() {
        //...
    }
}
```

app.cpp

```cpp
//应用程序开发人员
class Application {
public:
    bool Step2() {
        //...
    }
    void Step4() {
        //...
    }
}
```

主程序

```cpp
int main() {
    Library lib();
    Application app();
    
    lib.Step1();
    
    if(app.Step2()) {
        lib.Step3();
    }
    for(int i = 0; i < 4; i++) {
        app.Step4();
    }
    lib.Step5();
}
```



## 用模板方法的代码重构为

<img src="image-20200710191917988.png" alt="image-20200710191917988" style="zoom:50%;" />

lib.cpp

```cpp
class Library {
public:
    void Run() {
        Step1();
        if(Step2()) {
            Step3();
        }
        for(int i = 0; i < 4; i++) {
            Step4();
        }
        Step5();
    }
    
    virtual ~Library() {}
protected:
    void Step1() { //稳定
        //...
    }
    void Step3() { //稳定
        //...
    }
    void Step5() { //稳定
        //...
    }
    
    virtual bool Step2() = 0; //变化
    virtual void Step4() = 0; //变化
}
```

app.cpp

```cpp
//应用程序开发人员
class Application: public Library {
protected:
    virtual bool Step2() {
        //...
    }
    virtual void Step4() {
        //...
    }
};
```

主程序

```cpp
int main() {
    Library* pLib = new Application();
    lib.Run();
    delete pLib;
}
```



## 总结

这两者的区别在于早绑定和晚绑定。前者属于早绑定，而template method属于晚绑定

<img src="image-20200710192039991.png" alt="image-20200710192039991" style="zoom:50%;" />

定义一个操作中的算法的骨架 (稳定)，而将一些步骤延迟 (变化)到子类中。Template Method使得子类可以不改变 (复用)一个算法的结构即可重定义(override 重写)该算法的某些特定步骤。

