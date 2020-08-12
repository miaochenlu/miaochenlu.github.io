---
title: Decorator
date: 2020-08-07 14:15:57
index_img: /img/image-20200807160507597.png
tags: Design Patterns
categories: Design Patterns

---

## Decorator属于"单一职责"模式

在软件组件的设计中，如果责任划分的不清晰，使用继承得到的结果往往是随着需求的变化，子类急剧膨胀，同时充斥着重复代码， 这时候的关键是划清责任。

* 典型模式

  • Decorator

  • Bridge

## 动机

在某些情况下我们可能会"过度地使用继承来扩展对象的功能"， 由于继承为类型引入的**静态特质**，使得这种扩展方式缺乏灵活性； 并且随着子类的增多（扩展功能的增多），各种子类的组合（扩展功能的组合）会导致更多子类的膨胀。

如何使"对象功能的扩展"能够根据需要来动态地实现？同时避免"扩展功能的增多"带来的子类膨胀问题？从而使得任何"功能扩展变化"所导致的影响将为最低？

所谓静态特质, 比如在CryptoNetworkStream的Read函数中定死了调用NetworkStream::Read(), 而我们重构后的CryptoStream中的Read函数调用stream->Read(), 支持多态，可以产生变化。



## 模式定义

动态（组合）地给一个对象增加一些额外的职责。就增加功能而言，Decorator模式比生成子类（继承）更为灵活（消除重复代码 & 减少子类个数）。



## 代码示例

下面给出一个Stream代码示例，其继承关系如下所示

<img src="image-20200807143722316.png" alt="image-20200807143722316" style="zoom:30%;" />

```cpp
//业务操作
class Stream {
public:
    virtual char Read(int number) = 0;
    virtual void Seek(int position) = 0;
    virtual void Write(char data) = 0;
    
    virtual ~Stream() {}
};

//主体类
class FileStream: public Stream {
public:
    virtual char Read(int number) {
        //读文件流
	}
    
    virtual void Seek(int position) {
        //定位文件流
    }
    
    virtual void Write(char data) {
        //写文件流
    }
};

class NetworkStream: public Stream {
public:
    virtual char Read(int number) {
        //读网络流
	}
    
    virtual void Seek(int position) {
        //定位网络流
    }
    
    virtual void Write(char data) {
        //写网络流
    }
};

class MemoryStream: public Stream {
public:
    virtual char Read(int number) {
        //读内存流
	}
    
    virtual void Seek(int position) {
        //定位内存流
    }
    
    virtual void Write(char data) {
        //写内存流
    }
};

//扩展操作
class CryptoFileStream: public FileStream {
public:
    virtual char Read(int number) {
        //额外的加密操作...
        FileStream::Read(number);
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        FileStream::Seek(position);
        //额外的加密操作
    }
    
    virtual void Write(byte data) {
        //额外的加密操作...
        FileStream::Write(data);
        //额外的加密操作...
    }
};

class CryptoNetworkStream: public NetworkStream {
public:
    virtual char Read(int number) {
        //额外的加密操作...
        NetworkStream::Read(number);
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        NetworkStream::Seek(position);
        //额外的加密操作
    }
    
    virtual void Write(byte data) {
        //额外的加密操作...
        NetworkStream::Write(data);
        //额外的加密操作...
    }
};

class CryptoMemoryStream: public MemoryStream {
public:
    virtual char Read(int number) {
        //额外的加密操作...
        MemoryStream::Read(number);
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        MemoryStream::Seek(position);
        //额外的加密操作
    }
    
    virtual void Write(byte data) {
        //额外的加密操作...
        MemoryStream::Write(data);
        //额外的加密操作...
    }
};

class BufferedFileStream: public FileStream {
    //...
};

class BufferedNetworkStream: public NetworkStream {
    //...
};

class BufferedMemoryStream: public MemoryStream {
    //...
};

class CryptoBufferedFileStream: public FileStream {
public:
    virtual char Read(int number) {
        //额外的加密操作...
        //额外的缓冲操作...
        FileStream::Read(number);//读文件流
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        //额外的缓冲操作...
        FileStream::Seek(position);//定位文件流
        //额外的加密操作...
        //额外的缓冲操作...
    }
    
    virtual void Write(int position) {
        //额外的加密操作...
        //额外的缓冲操作...
        FileStream::Write(position);//写文件流
        //额外的加密操作...
        //额外的缓冲操作...
    }
};

void Process() {
    //编译时装配
    CryptoFileStream* fs1 = new CryptoFileStream();
    
    BufferedFileStream* fs2 = new BufferedFileStream();
    
    CryptoBufferedFileStream* fs3 = new CryptoBufferedFileStream();
}
```

这样设计的代码存在大量的冗余，比如对于不同的stream, 无论是FileStream还是NetworkStream还是MemoryStream, 他们的加密操作都是一样的。



* 重构

```cpp
//扩展操作
class CryptoFileStream {
    FileStream* stream;//=new FileStream()
public:
    virtual char Read(int number) {
        //额外的加密操作...
        stream->Read(number);
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        stream->Seek(position);
        //额外的加密操作
    }
    
    virtual void Write(byte data) {
        //额外的加密操作...
        stream->Write(data);
        //额外的加密操作...
    }
};

class CryptoNetworkStream {
    NetworkStream* stream;	//=new NetworkStream();
public:
    virtual char Read(int number) {
        //额外的加密操作...
        stream->Read(number);
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        stream->Seek(position);
        //额外的加密操作
    }
    
    virtual void Write(byte data) {
        //额外的加密操作...
        stream->Write(data);
        //额外的加密操作...
    }
};

class CryptoMemoryStream {
    MemoryStream* stream;	//=new MemoryStream();
public:
    virtual char Read(int number) {
        //额外的加密操作...
        stream->Read(number);
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        stream->Seek(position);
        //额外的加密操作
    }
    
    virtual void Write(byte data) {
        //额外的加密操作...
        stream->Write(data);
        //额外的加密操作...
    }
};
```

* 继续重构

```cpp
//扩展操作
class CryptoStream: public Stream {
    Stream* stream;//...
public:
    CryptoStream(Stream* stm): stream(stm) {
        
    }
    virtual char Read(int number) {
        //额外的加密操作...
        stream->Read(number);
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        stream->Seek(position);
        //额外的加密操作
    }
    
    virtual void Write(byte data) {
        //额外的加密操作...
        stream->Write(data);
        //额外的加密操作...
    }
};

class BufferedStream: public Stream {
    Stream* stream; //...
    //...
};

void Process() {
    //运行时装配
    FileStream* s1 = new FileStream();
    CryptoStream* s2 = new CryptoStream(s1);
    BufferedStream* s3 = new BufferedStream(s1);
    BufferedStream* s4 = new BufferedStream(s2);
}
```

很神奇，CryptoStream既有Stream基类，也有Stream字段。这里还需要继承Stream是因为他定义了基类的接口规范



* 继续重构

CryptoStream, BufferedStream都有Stream这个字段，把他们提到一个中间类中，

设置一个DecoratorStream中间类

```cpp
//扩展操作
class DecoratorStream: public Stream {
protected:
    Stream* stream; //...
    DecoratorStream(Stream* stm): DecoratorStream(stm) {
        
    }
}
class CryptoStream: public DecoratorStream {
public:
    CryptoStream(Stream* stm): DecoratorStream(stm) {}
    virtual char Read(int number) {
        //额外的加密操作...
        stream->Read(number);
    }
    
    virtual void Seek(int position) {
        //额外的加密操作...
        stream->Seek(position);
        //额外的加密操作
    }
    
    virtual void Write(byte data) {
        //额外的加密操作...
        stream->Write(data);
        //额外的加密操作...
    }
};

class BufferedStream: public DecoratorStream {
    //...
};

void Process() {
    //运行时装配
    FileStream* s1 = new FileStream();
    CryptoStream* s2 = new CryptoStream(s1);
    BufferedStream* s3 = new BufferedStream(s1);
    BufferedStream* s4 = new BufferedStream(s2);
}
```

新的类关系

<img src="image-20200807143823919.png" alt="image-20200807143823919" style="zoom:30%;" />



## 结构

<img src="image-20200807160507597.png" alt="image-20200807160507597" style="zoom:50%;" />



## 总结

* 通过采用组合而非继承的手法， Decorator模式实现了在运行时动态扩展对象功能的能力，而且可以根据需要扩展多个功能。避免 了使用继承带来的"灵活性差"和"多子类衍生问题"。

* Decorator类在接口上表现为is-a Component的继承关系，即Decorator类继承了Component类所具有的接口。但在实现上又 表现为has-a Component的组合关系，即Decorator类又使用了 另外一个Component类。

* Decorator模式的目的并非解决"多子类衍生的多继承"问题，Decorator模式应用的要点在于解决"主体类在多个方向上的扩展 功能"——是为"装饰"的含义。