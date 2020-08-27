---
title: Builder
date: 2020-08-27 13:57:46
index_img: /img/image-20200827143019216.png
tags: Design Patterns
categories: Design Patterns
---





## Builder属于对象创建模式

通过对象创建模式绕开`new`, 来避免对象创建 (`new`) 过程中所导致的紧耦合(依赖具体类 )，从而支持对象创建的稳定。他是接口抽象之后的第一步工作。

典型模式有

* Factory Method
* Abstract Factory
* Prototype
* BuilderPrototype属于对象创建模式



## 动机

* 在软件系统中，有时候面临着"一个复杂对象"的创建工作，其通常由各个部分的子对象用一定的算法构成；由于需求的变化，这 个复杂对象的各个部分经常面临着剧烈的变化，但是将它们组合在 一起的算法却相对稳定。

* 如何应对这种变化？如何提供一种"封装机制"来隔离出"复杂对象的各个部分"的变化，从而保持系统中的"稳定构建算法"不 随着需求改变而改变？



## 模式定义

将一个复杂对象的构建与其表示相分离，使得同样的构建过 程(稳定)可以创建不同的表示(变化)

## 代码示例

```cpp
class House {
public:
    void Init() {
        this->BuildPart1();
        
        for(int i = 0; i < 4; i++) {
            this->BuildPart2();
        }
        
        bool flag = this->BuildPart3();
        
        if(flag) {
            this->BuildPart4();
        }
        
        this->BuildPart5();
    }
    virtual ~House() {}
protected:
    virtual void BuildPart1() = 0;
    virtual void BuildPart2() = 0;
    virtual void BuildPart3() = 0;
    virtual void BuildPart4() = 0;
    virtual void BuildPart5() = 0;
}

class StoneHouse: public House {
protected:
    virtual void BuildPart1() {}
    virtual void BuildPart2() {}
    virtual void BuildPart3() {}
    virtual void BuildPart4() {}
    virtual void BuildPart5() {}
}
```

我们会发现这份代码非常像template method

{% note warning %}

有个疑问就是，既然是创建对象， 能不能把Init()函数改成构造函数

{% endnote %}



{% note success %}

答案是不行，构造函数中调用虚函数用的是静态绑定

{% endnote %}



* 重构

拆分House类, HouseBuilder专管构建

```cpp
class House {
    //...
}

class HouseBulder {
    House* pHouse;
public:
    void Init() {
        this->BuildPart1();
        
        for(int i = 0; i < 4; i++) {
            this->BuildPart2();
        }
        
        bool flag = this->BuildPart3();
        
        if(flag) {
            this->BuildPart4();
        }
        
        this->BuildPart5();
    }
    
    House* getResult() {
        return pHouse;
    }
    
    virtual ~HouseBuilder() {}
protected:
    virtual void BuildPart1() = 0;
    virtual void BuildPart2() = 0;
    virtual void BuildPart3() = 0;
    virtual void BuildPart4() = 0;
    virtual void BuildPart5() = 0;
}
```

* 再重构 

```cpp
class House {
    //...
}

class HouseBulder {
public:
    House* getResult() {
        return pHouse;
    }
    
    virtual ~HouseBuilder() {}
protected:
    House* pHouse;
    virtual void BuildPart1() = 0;
    virtual void BuildPart2() = 0;
    virtual void BuildPart3() = 0;
    virtual void BuildPart4() = 0;
    virtual void BuildPart5() = 0;
}

class HouseDirector {
public:
    HouseBuilder* pHouseBuilder;
	House* Construct() {
        pHouseBuilder->BuildPart1();
        
        for(int i = 0; i < 4; i++) {
            pHouseBuilder->BuildPart2();
        }
        
        bool flag = pHouseBuilder->BuildPart3();
        
        if(flag) {
            pHouseBuilder->BuildPart4();
        }
        
        pHouseBuilder->BuildPart5();
        return pHouseBuilder->getResult();
    }
}
```





## 结构

<img src="image-20200827143019216.png" alt="image-20200827143019216" style="zoom:67%;" />



## 总结

* Builder 模式主要用于“分步骤构建一个复杂的对象"。在这其中 “分步骤"是一个稳定的算法，而复杂对象的各个部分则经常变化。

* 变化点在哪里，封装哪里—— Builder模式主要在于应对"复杂对 象各个部分"的频繁需求变动。其缺点在于难以应对"分步骤构建 算法"的需求变动。

* 在Builder模式中，要注意不同语言中构造器内调用虚函数的差别 （C++ vs. C#) 。