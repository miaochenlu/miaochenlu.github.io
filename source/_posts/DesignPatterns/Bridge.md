---
title: Bridge 桥模式
date: 2020-08-11 14:48:03
index_img: /img/image-20200811150059187.png
tags: Design Patterns
categories: Design Patterns
---

## Bridge属于"单一职责"模式

在软件组件的设计中，如果责任划分的不清晰，使用继承得到的结果往往是随着需求的变化，子类急剧膨胀，同时充斥着重复代码， 这时候的关键是划清责任。

* 典型模式

  • Decorator

  • Bridge



## 动机



由于某些类型的固有的实现逻辑，使得它们具有<u>两个变化的维度， 乃至多个纬度</u>的变化。

如何应对这种"多维度的变化"？如何利用面向对象技术来使得类型可以轻松地沿着两个乃至多个方向变化，而不引入额外的复杂度？



## 模式定义

将抽象部分(业务功能)与实现部分(平台实现)分离，使他们都可以独立地变化

## 代码示例

一个通信模块Messager。根据平台分为PC, Mobile，派生出两个子类PCMessagerBase, MobileMessagerBase。根据业务分成perfect和lite, 前面的两个子类又派生出PCMessagerLite, PCMessagerPerfect, MobileMessagerLite, MobileMessagerPerfect.

如果设平台有n中选择，业务有m中，那么类的数目为$1 + n + m * n$

```cpp
class Messager {
public:
    virtual void Login(string username, string password) = 0;
    virtual void SendMessage(string message) = 0;
    virtual void SendPicture(Image image) = 0;
    
    virtual void PlaySound() = 0;
    virtual void DrawShape() = 0;
    virtual void WriteText() = 0;
    virtual void Connect() = 0;
    
    virtual ~Message() {}
}

//平台实现
class PCMessagerBase: public Messager {
public:
    virtual void PlaySound() {
        //******
    }
    virtual void DrawShape() {
        //******
    }
    virtual void WriteText() {
        //******
    }
    virtual void Connect() {
        //******
    }
};

class MobileMessagerBase: public Messager {
public:
    virtual void PlaySound() {
        //******
    }
    virtual void DrawShape() {
        //******
    }
    virtual void WriteText() {
        //******
    }
    virtual void Connect() {
        //******
    }
};
 
//业务抽象
class PCMessagerLite: public PCMessagerBase {
public:
    virtual void Login(string username, string password) {
        PCMessagerBase::Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
        PCMessagerBase::WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
        PCMessagerBase::DrawShape();
        //......
    }
}

class PCMessagerPerfect: public PCMessagerBase {
public:
    virtual void Login(string username, string password) {
		PCMessagerBase::PlaySound();
        //******
        PCMessagerBase::Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
		PCMessagerBase::PlaySound();
        //******
        PCMessagerBase::WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
        PCMessagerBase::PlaySound();
        //******
        PCMessagerBase::DrawShape();
        //......
    }
}

class MobileMessagerLite: public PCMessagerBase {
public:
    virtual void Login(string username, string password) {
        MobileMessagerBase::Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
        MobileMessagerBase::WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
        MobileMessagerBase::DrawShape();
        //......
    }
}

class MobileMessagerPerfect: public PCMessagerBase {
public:
    virtual void Login(string username, string password) {
		MobileMessagerBase::PlaySound();
        //******
        MobileMessagerBase::Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
		MobileMessagerBase::PlaySound();
        //******
        MobileMessagerBase::WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
        MobileMessagerBase::PlaySound();
        //******
        MobileMessagerBase::DrawShape();
        //......
    }
}
```



但是这样设置的类有很多的结构性重复。比如PCMessagerPerfect, MobileMessagerPerfect。

* 重构

将PCMessagerLite, PCMessagerPerfect, MobileMessagerLite, MobileMessagerPerfect的继承改为组合。

```cpp
class Messager {
public:
    virtual void Login(string username, string password) = 0;
    virtual void SendMessage(string message) = 0;
    virtual void SendPicture(Image image) = 0;
    
    virtual void PlaySound() = 0;
    virtual void DrawShape() = 0;
    virtual void WriteText() = 0;
    virtual void Connect() = 0;
    
    virtual ~Message() {}
}

//平台实现
class PCMessagerBase: public Messager {
public:
    virtual void PlaySound() {
        //******
    }
    virtual void DrawShape() {
        //******
    }
    virtual void WriteText() {
        //******
    }
    virtual void Connect() {
        //******
    }
};

class MobileMessagerBase: public Messager {
public:
    virtual void PlaySound() {
        //******
    }
    virtual void DrawShape() {
        //******
    }
    virtual void WriteText() {
        //******
    }
    virtual void Connect() {
        //******
    }
};
 
//业务抽象
class MessagerLite {
    Messager* messager;
public:
    virtual void Login(string username, string password) {
        messager->Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
        messager->WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
        messager->DrawShape();
        //......
    }
}

class MessagerPerfect {
    Messager* messager;
public:
    virtual void Login(string username, string password) {
		messager->PlaySound();
        //******
        messager->Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
		messager->PlaySound();
        //******
        messager->WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
       	messager->PlaySound();
        //******
        messager->DrawShape();
        //......
    }
}
```

注意，这样修改之后并不是完整的，因为MessagerLite和MessagerPerfect没有了PlaySound, Connect...这些函数

* 继续重构

可以看到平台类在重载PlaySound, PlaySound, WriteText, Connect这几个函数。而业务类在重载Login, SendMessage, SendPicture这几个函数。因此把这两块函数放在一个类里面是不合适的，我们把他们拆分成两个类

```cpp
class Messager {
public:
    virtual void Login(string username, string password) = 0;
    virtual void SendMessage(string message) = 0;
    virtual void SendPicture(Image image) = 0;
    
    virtual ~Message() {}
}

class MessagerImp {
public:
    virtual void PlaySound() = 0;
    virtual void PlaySound() = 0;
    virtual void WriteText() = 0;
    virtual void Connect() = 0;
    
    virtual ~MessageImp() {}
}

//平台实现
class PCMessagerBase: public MessagerImp {
public:
    virtual void PlaySound() {
        //******
    }
    virtual void DrawShape() {
        //******
    }
    virtual void WriteText() {
        //******
    }
    virtual void Connect() {
        //******
    }
};

class MobileMessagerBase: public MessagerImp {
public:
    virtual void PlaySound() {
        //******
    }
    virtual void DrawShape() {
        //******
    }
    virtual void WriteText() {
        //******
    }
    virtual void Connect() {
        //******
    }
};
 
//业务抽象
class MessagerLite: public Messager {
    MessagerImp* messagerImp;	//...
public:
    virtual void Login(string username, string password) {
        messagerImp->Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
        messagerImp->WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
        messagerImp->DrawShape();
        //......
    }
}

class MessagerPerfect: public Messager {
    MessagerImp* messagerImp;	//...
public:
    virtual void Login(string username, string password) {
		messagerImp->PlaySound();
        //******
        messagerImp->Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
		messagerImp->PlaySound();
        //******
        messagerImp->WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
       	messagerImp->PlaySound();
        //******
        messagerImp->DrawShape();
        //......
    }
}
```

* 再重构

可以看到MessageLite和MessagerPerfect这两个类里面都有MessagerImp字段。根据重构的原则，应该将他往上提到Messager类中。

最终类的数目编程$1+n+m$

```cpp
class Messager {
protected:
    MessagerImp* messagerImp;
public:
    virtual void Login(string username, string password) = 0;
    virtual void SendMessage(string message) = 0;
    virtual void SendPicture(Image image) = 0;
    
    virtual ~Message() {}
}

class MessagerImp {
public:
    virtual void PlaySound() = 0;
    virtual void PlaySound() = 0;
    virtual void WriteText() = 0;
    virtual void Connect() = 0;
    
    virtual ~MessageImp() {}
}

//平台实现
class PCMessagerBase: public MessagerImp {
public:
    virtual void PlaySound() {
        //******
    }
    virtual void DrawShape() {
        //******
    }
    virtual void WriteText() {
        //******
    }
    virtual void Connect() {
        //******
    }
};

class MobileMessagerBase: public MessagerImp {
public:
    virtual void PlaySound() {
        //******
    }
    virtual void DrawShape() {
        //******
    }
    virtual void WriteText() {
        //******
    }
    virtual void Connect() {
        //******
    }
};
 
//业务抽象
class MessagerLite: public Messager {
public:
    virtual void Login(string username, string password) {
        messagerImp->Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
        messagerImp->WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
        messagerImp->DrawShape();
        //......
    }
}

class MessagerPerfect: public Messager {
public:
    virtual void Login(string username, string password) {
		messagerImp->PlaySound();
        //******
        messagerImp->Connect();
        //......
    }
    
    virtual void SendMessage(string message) {
		messagerImp->PlaySound();
        //******
        messagerImp->WriteText();
        //......
    }
    
    virtual void SendPicture(Image image) {
       	messagerImp->PlaySound();
        //******
        messagerImp->DrawShape();
        //......
    }
}
```



## 结构



<img src="image-20200811150059187.png" alt="image-20200811150059187" style="zoom:50%;" />



## 总结

* Bridge模式使用"对象间的组合关系"解耦了抽象和实现之间固有的绑定关系，使得抽象和实现可以沿着各自的维度来变化。所谓 抽象和实现沿着各自纬度的变化，即"子类化"它们。

* Bridge模式有时候类似于多继承方案，但是多继承方案往往违背单一职责原则（即一个类只有一个变化的原因），复用性比较差。 Bridge模式是比多继承方案更好的解决方法。

* Bridge模式的应用一般在"两个非常强的变化维度"，有时一个 类也有多于两个的变化维度，这时可以使用Bridge的扩展模式。