---
title: Strategy Method
date: 2020-08-05 15:53:57
index_img: /img/image-20200805135526277.png
tags: Design Patterns
---

## 动机

在软件构建过程中，某些对象使用的算法可能多种多样，经常改变，如果将这些算法都编码到对象中，将会使对象变得异常复杂； 而且有时候支持不使用的算法也是一个性能负担

如何在运行时根据需要透明地更改对象的算法？将算法与对象本身解耦，从而避免上述问题？



## Strategy Method定义

定义一系列算法，把它们一个个封装起来，并且使它们可互 相替换（变化）。该模式使得算法可独立于使用它的客户程 序(稳定)而变化（扩展，子类化）。



## 代码示例

考虑一下计算税的例子

* 第一种实现方式

```cpp
enum TaxBase {
    CN_Tax,
    US_Tax,
    DE_Tax
};

class SalesOrder {
    TaxBase tax;
public:
    double CalculateTax() {
        //...
        
        if(tax == CN_Tax) {
            //CN*****
        } else if(tax == US_Tax) {
            //US*****
        } else if(tax == DE_Tax) {
            //DE*****
        }
        //***
    }
}
```

有些时候，静态地去观察是看不出来的，加入时间轴，考虑到未来的动态变化，问题就会暴露出来。考虑上述例子，如果要加入一个国家，比如法国，那么我们不仅要在`TaxBase`中中增加一个`FR_Tax`, 还需要在`SalesOrder`中增加判断语句。这样的更改违背了开闭原则： 对扩展开放，对修改封闭 (避免去修改源代码

```cpp
enum TaxBase {
    CN_Tax,
    US_Tax,
    DE_Tax,
    /***加入France***/
    FR_Tax,
};

class SalesOrder {
    TaxBase tax;
public:
    double CalculateTax() {
        //...
        
        if(tax == CN_Tax) {
            //CN*****
        } else if(tax == US_Tax) {
            //US*****
        } else if(tax == DE_Tax) {
            //DE*****
        }
        /***加入France***/
        else if(tax == FR_Tax) {
          
        }
        //***
    }
}
```



* 考虑另一种实现方式

```cpp
class TaxStrategy {
public:
    virtual double Calculate(const Context& context) = 0;
    virtual ~TaxStrategy() {}
};

class CNTax: public TaxStrategy {
public:
    virtual double Calculate(const Context& context) {
        //...
    }
};

class USTax: public TaxStrategy {
    virtual double Calculate(const Context& context) {
        //...
    }
}
class DETax: public TaxStrategy {
public:
    virtual double Calculate(const Context& context) {
        //...
    }
}
```

```cpp
class SalesOrder {
private:
    TaxStrategy* strategy;
public:
    SalesOrder(StrategyFactory* strategyFactory) {
        this->strategy = strategyFactory->NewStrategy();
    }
    
    ~SalesOrder() {
        delete this->strategy;
    }
    
    public double CalculateTax() {
        //...
        Context context();
        
        double val = 
            	srtategy->Calculate(context);//多态调用
    }
}
```

如果要增加法国，只需增加一个`FRTax`类

```cpp
class FRTax: public TaxStrategy {
public:
    virtual doubel Calculate(const Context& context) {
        //...
    }
}
```

这是一种扩展的做法，在这里只需要增加一个FRTax类，不需要更改SalesOrder。

复用性一般是指二进制层面下的复用性，如果使用第一种方法，那么整个系统需要重新编译，重新测试。虽然修改后保留了一部分原来的代码，但是这个并不是复用。而策略方法不需要重新编译之前编译好的文件，只需要重新编译FRTax这个文件



## 结构



![image-20200805135526277](image-20200805135526277.png) 



## 总结

* Strategy及其子类为组件提供了一系列可重用的算法，从而可以使 得类型在运行时方便地根据需要在各个算法之间进行切换。

* Strategy模式提供了用条件判断语句以外的另一种选择，消除条件 判断语句，就是在解耦合。含有许多条件判断语句的代码通常都需 要Strategy模式。

* 如果Strategy对象没有实例变量，那么各个上下文可以共享同一个 Strategy对象，从而节省对象开销





