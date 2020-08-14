---
title: Factory Method 工厂模式
date: 2020-08-14 13:51:37
index_img: /img/image-20200814150354579.png
tags: Design Patterns
categories: Design Patterns
key: DP-factory
---



## Factory Method属于对象创建模式

通过对象创建模式绕开`new`, 来避免对象创建 (`new`) 过程中所导致的紧耦合(依赖具体类 )，从而支持对象创建的稳定。他是接口抽象之后的第一步工作。

典型模式有

* Factory Method
* Abstract Factory
* Prototype
* Builder



## 动机

在软件系统中，经常面临着创建对象的工作；由于需求的变化， 需要创建的对象的具体类型经常变化。

如何应对这种变化？如何绕过常规的对象创建方法(new)，提供一 种"封装机制"来避免客户程序和这种"具体对象创建工作"的紧耦合？



## 模式定义

定义一个用于创建对象的接口，让子类决定实例化哪一个类。 Factory Method使得一个类的实例化延迟（目的：解耦， 手段：虚函数）到子类。



## 代码示例

初始代码，一个文件分割器。

这个代码会有什么问题呢？要从变化的场景去看。

```cpp
class MainForm: public Form {
    TextBox* txtFilePath;
    TextBox* txtFileNumber;
    ProgressBar* progressBar;
public:
    void Button1_Click() {
		FileSplitter* splitter = new FileSplitter();
        splitter->split;
    }
}
```

```cpp
class FileSpitter {
public:
    void split() {
        //...
    }
}
```



最开始我们讲过一个原则：面向接口的编程。一个对象的类型应该声明成抽象类或者接口，而不应该声明成具体的类。如果声明成具体的类，就把他定死了，无法应对未来的变化。



假设我们产生了新的需求：

需要支持二进制文件，txt文件，图片文件，视频文件的分割

```cpp
class ISplitter { //接口
public:
    virtual void split() = 0;
    virtual ~ISplitter() {}
};
class BinarySpliter: public ISplitter {
};

class TxtSplitter: public ISplitter {
};

class PictureSplitter: public ISplitter {
};

class VideoSplitter: public ISplitter {  
};
```

```cpp
class MainForm: public Form {
    TextBox* txtFilePath;
    TextBox* txtFileNumber;
    ProgressBar* progressBar;
public:
    void Button1_Click() {
/*******************问题产生了******************/
		ISplitter* splitter = new BinarySplitter(); //??????怎么又依赖细节了？？？
        splitter->split;
    }
}
```

{% note warning %}

`new`不能new抽象类，`new`只能new具体类。但是我们需要的是多态`new`。怎么解决呢？

{% endnote %}

* 开始重构

```cpp
class SplitterFactory {
public:
    ISplitter* CreateSplitter() {
        //return new ISplitter(...);//这样也是不对的，因为不能new抽象类
        return new BinarySplitter();
    }
}
class ISplitter { //接口
public:
    virtual void split() = 0;
    virtual ~ISplitter() {}
};
```

```cpp
class MainForm: public Form {
    TextBox* txtFilePath;
    TextBox* txtFileNumber;
    ProgressBar* progressBar;
public:
    void Button1_Click() {
		SplitterFactory factory;
		ISplitter* splitter = factory.CreateSplitter();
        splitter->split;
    }
}
```

但是这种实现根本没有解决问题，`ISplitter* splitter = factory.CreateSplitter();`依然依赖于具体的类BinarySplitter

* 继续重构

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
class MainForm: public Form {
    TextBox* txtFilePath;
    TextBox* txtFileNumber;
    ProgressBar* progressBar;
public:
    void Button1_Click() {
		SplitterFactory* factory;
//多态了
		ISplitter* splitter = factory->CreateSplitter();
        splitter->split;
    }
}
```



* 继续完善

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



## 结构

<img src="image-20200814150354579.png" alt="image-20200814150354579" style="zoom:50%;" />

## 总结

* Factory Method模式用于隔离类对象的使用者和具体类型之间的耦合关系。面对一个经常变化的具体类型，紧耦合关系(new)会导 致软件的脆弱。

* Factory Method模式通过面向对象的手法，将所要创建的具体对 象工作延迟到子类，从而实现一种扩展（而非更改）的策略，较好地解决了这种紧耦合关系。

* Factory Method模式解决"单个对象"的需求变化。缺点在于要求创建方法/参数相同。

