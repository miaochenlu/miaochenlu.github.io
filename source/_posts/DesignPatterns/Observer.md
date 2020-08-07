---
title: Observer 观察者模式
date: 2020-08-06 14:22:25
index_img: /img/image-20200806160700160.png
tags: Design Patterns
categories: Design Patterns
---



## 动机

在软件构建过程中，我们需要为某些对象建立一种"通知依赖关系" 

<u>一个对象（目标对象）的状态发生改变，所有的依赖对象（观察者对象）都将得到通知。如果这样的依赖关系过于紧密， 将使软件不能很好地抵御变化</u>。

使用面向对象技术，可以将这种依赖关系弱化，并形成一种稳定的依赖关系。从而实现软件体系结构的松耦合。



## 模式定义

定义对象间的一种<u>***一对多***</u>（变化）的依赖关系，以便当一个 对象(Subject)的状态发生改变时，所有依赖于它的对象都得到通知并<u>***自动更新***</u>。



## 代码示例

考虑一个文件分割器程序，将一个大文件分割成几个小文件。

我们有一个主界面MainForm (在这里是一个观察者), 里面有两个对象，一个是文件路径，一个是分割成的文件都个数。当button click时就会调用filesplitter将文件分割

代码如下

```cpp
class MainForm: public Form {
    TextBox* txtFilePath;
    TextBox* txtFileNumber;
public:
    void Button1_Click() {
        string filePath = txtFilePath->getText();
        int number = atoi(txtFileNumber->getText().c_str());
        
        FileSplitter splitter(filePath, number);
        splitter.split();
    }
}
```

```cpp
class FileSplitter {
    string m_filePath;
    int m_fileNumber;
public:
    FileSplitter(const strign& filePath, int fileNumber):
    	m_filePath(filePath),
    	m_fileNumber(fileNumber) {
            
    }
    
    void split() {
        //1. 读取大文件
        
        //2. 分批次向小文件中写入
        for(int i = 0; i < m_fileNumber; i++) {
            //...
        }
    }
}
```



#### 需求1：文件分割进度条

* 第一种实现方式

```cpp
class MainForm: public Form {
    TextBox* txtFilePath;
    TextBox* txtFileNumber;
/***增加progress bar***/
    ProgressBar* progressBar;
public:
    void Button1_Click() {
        string filePath = txtFilePath->getText();
        int number = atoi(txtFileNumber->getText().c_str());
        
        FileSplitter splitter(filePath, number);
        splitter.split();
    }
}
```



```cpp
class FileSplitter {
    string m_filePath;
    int m_fileNumber;
/***增加progres bar***/
    ProgressBar* m_progressBar;
public:
/***增加初始化参数***/
    FileSplitter(const strign& filePath, int fileNumber, ProgressBar* progressBar):
    	m_filePath(filePath),
    	m_fileNumber(fileNumber) 
        m_progressBar(progressBar){
            
    }
    
    void split() {
        //1. 读取大文件
        
        //2. 分批次向小文件中写入
        for(int i = 0; i < m_fileNumber; i++) {
            //...
/***更新progress bar***/
            if(m_progressBar != NULL) {
                m_progressBar->setValue((i + 1) / m_fileNumber);
            }
        }
    }
}
```

{% note warning%}

但是这种实现方式违反了<u>依赖倒置原则</u>。FileSplitter依赖ProgressBar 这样一种具体的实现细节，但是进度条的种类和展现形式是变化的，如果我们想将进度条换一种展现形式，比如不断打点，那么就会面临需求变更的困扰。

{% endnote %}



* 第二种实现方式

```cpp
class IProgress {
public:
    virtual void DoProgresss(float value) = 0;
    virtual ~IProgress() {}
}
class FileSplitter {
    string m_filePath;
    int m_fileNumber;
    //ProgressBar* m_progressBar; 这是一个通知控件
    IProgress* m_iprogress; //抽象通知机制
public:
    FileSplitter(const strign& filePath, int fileNumber, IProgress* iprogress):
    	m_filePath(filePath),
    	m_fileNumber(fileNumber) 
        m_iprogress(iprogress){
            
    }
    
    void split() {
        //1. 读取大文件
        
        //2. 分批次向小文件中写入
        for(int i = 0; i < m_fileNumber; i++) {
            //...
			
            if(m_iprogress != NULL) {
                float progressValue = m_fileNumber;
                progressValue = (i + 1) / progressValue;
                m_iprogress->DoProgress(progressValue); //更新进度条
            }
        }
    }
}
```

```cpp
class MainForm: public Form, public IProgress{
    TextBox* txtFilePath;
    TextBox* txtFileNumber;
    ProgressBar* progressbar;
public:
    void Button1_Click() {
        string filePath = txtFilePath->getText();
        int number = atoi(txtFileNumber->getText().c_str());
        
        FileSplitter splitter(filePath, number);
        splitter.split();
    }
    
    virtual void DoProgress(float value) {
        progressBar->setValue(value);
    }
}
```

C++支持多继承，但是不推荐使用多继承，因为会带来很多耦合的问题。但是有一种情况是推荐使用多继承的，一个主的继承类，其他都是接口  (单继承多实现), 这里就属于这种情况

* 再修改一下

```cpp
class IProgress {
public:
    virtual void DoProgresss(float value) = 0;
    virtual ~IProgress() {}
}
class FileSplitter {
    string m_filePath;
    int m_fileNumber;
    //ProgressBar* m_progressBar; 这是一个通知控件
    IProgress* m_iprogress; //抽象通知机制
public:
    FileSplitter(const strign& filePath, int fileNumber, IProgress* iprogress):
    	m_filePath(filePath),
    	m_fileNumber(fileNumber) 
        m_iprogress(iprogress){
            
    }
    
    void split() {
        //1. 读取大文件
        
        //2. 分批次向小文件中写入
        for(int i = 0; i < m_fileNumber; i++) {
            //...
			
            float progressValue = m_fileNumber;
            progressValue = (i + 1) / progressValue;
            onProgress(progressValue);
        }
    }
protected:
    void onProgress(float value) {
        if(m_iprogress != NULL) {
	        m_iprogress->DoProgress(value);	//更新进度条
        }
    }
}
```





#### 需求2: 多观察者

```cpp
class IProgress {
public:
    virtual void DoProgresss(float value) = 0;
    virtual ~IProgress() {}
}
class FileSplitter {
    string m_filePath;
    int m_fileNumber;
    List<IProgress*> m_iprogressList; //抽象通知机制, 支持多个观察者
public:
    FileSplitter(const strign& filePath, int fileNumber):
    	m_filePath(filePath),
    	m_fileNumber(fileNumber) {
            
    }
    
    void addIProgress(IProgress* iprogress) {
        m_iprogressList.add(iprogress);
    }
    
    void removeIProgress(IProgress* iprogress) {
        m_iprogressList.remove(iprogress);
    }
    
    
    void split() {
        //1. 读取大文件
        
        //2. 分批次向小文件中写入
        for(int i = 0; i < m_fileNumber; i++) {
            //...
			
            float progressValue = m_fileNumber;
            progressValue = (i + 1) / progressValue;
            onProgress(progressValue);
        }
    }
protected:
    void onProgress(float value) {
        List<IProgress*>::iterator it = m_iprogressList.begin();
        
		while(it != m_iprogressList.end()) {
            (*it)->DoProgress(value);
            it++;
        }
    }
}
```

```cpp
class MainForm: public Form, public IProgress{
    TextBox* txtFilePath;
    TextBox* txtFileNumber;
    ProgressBar* progressbar;
public:
    void Button1_Click() {
        string filePath = txtFilePath->getText();
        int number = atoi(txtFileNumber->getText().c_str());
        
        ConsoleNotifier cn;
        FileSplitter splitter(filePath, number);
        
        splitter.addIprogress(this);
        splitter.addIprogress(&cn);
        
        splitter.split();
    }
    
    virtual void DoProgress(float value) {
        progressBar->setValue(value);
    }
}

class ConsoleNotifier: public IProgress {
public:
    virtual void DoProgress(float value) {
        cout << ".";
    }
}
```



## 结构

<img src="image-20200806160700160.png" alt="image-20200806160700160" style="zoom: 50%;" />



## 总结

* 使用面向对象的抽象，Observer模式使得我们可以独立地改变目标与观察者，从而使二者之间的依赖关系达致松耦合。 

* 目标发送通知时，无需指定观察者，通知（可以携带通知信息作为参数）会自动传播。

* 观察者自己决定是否需要订阅通知，目标对象对此一无所知。 
* Observer模式是基于事件的UI框架中非常常用的设计模式，也是 MVC模式的一个重要组成部分。