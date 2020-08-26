---
title: Prototype 原型模式
date: 2020-08-26 12:50:58
index_img: /img/image-20200826132954596.png
tags: Design Patterns
categories: Design Patterns
---



## Prototype属于对象创建模式

通过对象创建模式绕开`new`, 来避免对象创建 (`new`) 过程中所导致的紧耦合(依赖具体类 )，从而支持对象创建的稳定。他是接口抽象之后的第一步工作。

典型模式有

* Factory Method
* Abstract Factory
* Prototype
* Builder





## 动机

* 这是factory method的动机

> 在软件系统中，经常面临着创建对象的工作；由于需求的变化， 需要创建的对象的具体类型经常变化。
>
> 如何应对这种变化？如何绕过常规的对象创建方法(new)，提供一 种"封装机制"来避免客户程序和这种"具体对象创建工作"的紧耦合？

* 这是prototype的动机

> 在软件系统中，经常面临着"<u>某些结构复杂的对象</u>"的创建工作；由于需求的变化， 这些对象经常面临着剧烈的变化，但是他们却拥有比较稳定一致的接口。
>
> 如何应对这种变化？如何向"客户程序(使用这些对象的程序)"隔离出这些"易变对象"，从而使得"依赖这些易变对象的客户程序"不随着需求改变而改变?
>
> （maybe类似游戏读档

非常相似是不是，知识prototype中面对的是结构复杂的对象



## 模式定义

使用原型实例指定创建对象的种类，然后通过<u>拷贝</u>这些原型来创建新的对象。



## 代码示例

#### 这是Factory method中使用的代码

```cpp
//抽象类
class ISplitter {
public:
    virtual void split() = 0;
    virtual ~ISplitter() {}
};
//工厂基类
class SplitterFactory {
public:
	virtual void ISplitter* CreateSplitter() = 0;
    virtual ~SplitterFactory(){}
    }
};
```

```cpp
//具体类
class BinarySpliter: public ISplitter {
};

class TxtSplitter: public ISplitter {
};

class PictureSplitter: public ISplitter {
};

class VideoSplitter: public ISplitter {  
};

//具体工厂
BinarySplitterFactory: public SplitterFactory {
public:
    virutal ISplitter* CreateSplitter() {
        return new BinarySplitter();
    }
};

TxtSplitterFactory: public SplitterFactory {
public:
    virutal ISplitter* CreateSplitter() {
        return new TxtSplitter();
    }
};

PictureSplitterFactory: public SplitterFactory {
public:
    virutal ISplitter* CreateSplitter() {
        return new PictureSplitter();
    }
};

VideoSplitterFactory: public SplitterFactory {
public:
    virutal ISplitter* CreateSplitter() {
        return new VideoSplitter();
    }
};
```

```cpp
class MainForm: public Form {
    SplitterFactory* factory;
public:
    MainForm(SplitterFactory* factory) {
        this->factory = factory;
    }
    
    void Button1_Click() {
//多态new
		ISplitter* splitter = factory->CreateSplitter();
        splitter->split;
    }
}
```



#### 在prototype中, 合并ISplitter, SplitterFactory

```cpp
class ISplitter {
public:
 	virtual void split() = 0;
    virtual ISplitter* clone() = 0; //通过clone自己来创建对象
    
    virtual ~ISplitter() {}
}
```

```cpp
//具体类
class BinarySpliter: public ISplitter {
public:
    virutal ISplitter* clone() {
        return new BinarySplitter(*this);
    }
};

class TxtSplitter: public ISplitter {
public:
    virutal ISplitter* clone() {
        return new TxtSplitter(*this);
    }
};

class PictureSplitter: public ISplitter {
public:
    virutal ISplitter* clone() {
        return new PictureSplitter(*this);
    }
};

class VideoSplitter: public ISplitter {  
public:
    virutal ISplitter* clone() {
        return new VideoSplitter(*this);
    }
};
```

```cpp
class MainForm: public Form {
    ISplitter* prototype;
public:
    MainForm(SplitterFactory* prototype) {
        this->prototype = prototype;//原型对象是供clone的
    }
    
    void Button1_Click() {
		ISplitter* splitter = prototype->clone();
        splitter->split;
    }
}
```





## 结构

<img src="image-20200826132954596.png" alt="image-20200826132954596" style="zoom:67%;" />



## 总结

* Prototype模式同样用于隔离类独享的使用者和具体类型(易变类)之间的耦合关系，它同样要求这些"易变类"拥有稳定的接口
* Prototype模式对于"如何创建易变类的尸体对象"采用"原型克隆"的方法来做，它使得我们可以非常灵活的动态创建"拥有某些稳定接口"的新对象--所需工作仅仅是注册一个新类的对象(即原型)，然后在任何需要的地方Clone
* Prototype模式中的clone方法可以利用某些框架中的序列化来实现深拷贝。

