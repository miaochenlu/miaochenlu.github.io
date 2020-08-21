---
title: AbstractFactory
date: 2020-08-21 13:51:40
index_img: /img/image-20200821143526358.png
tags: Design Patterns
categories: Design Patterns
---

## Abstract Factory属于对象创建模式

通过对象创建模式绕开`new`, 来避免对象创建 (`new`) 过程中所导致的紧耦合(依赖具体类 )，从而支持对象创建的稳定。他是接口抽象之后的第一步工作。

典型模式有

* Factory Method
* Abstract Factory
* Prototype
* Builder

## 动机

* 在软件系统中，经常面临着"<u>一系列相互依赖的对象</u>"的创建工作；同时，由于需求的变化，往往存在更多系列对象的创建工作。

* 如何应对这种变化？如何绕过常规的对象创建方法(new)，提供一种"封装机制"来避免客户程序和这种"多系列具体对象创建工作" 的紧耦合？





## 模式定义

提供一个接口，让该接口负责创建一系列"相关或者相互依赖的对象"，无需指定它们具体的类。

## 代码示例

任务是写一个数据访问层。数据库可能会有各种各样的变化。可能是sqlserver, oracel, mysql, 因此对应的类型就需要变化。但是下面的代码的new并不是多态new

```cpp
class EmployeeDAO{
    
public:
    vector<EmployeeDO> GetEmployees(){
        SqlConnection* connection =
            new SqlConnection();
        connection->ConnectionString = "...";

        SqlCommand* command =
            new SqlCommand();
        command->CommandText="...";
        command->SetConnection(connection);

        SqlDataReader* reader = command->ExecuteReader();
        while (reader->Read()){

        }
    }
};
```



* 重构

要支持多态new, 马上就想到了factory method.

```cpp
//数据库访问有关的基类
class IDBConnection{    
};

class IDBConnectionFactory{
public:
    virtual IDBConnection* CreateDBConnection()=0;
};

class IDBCommand{    
};
class IDBCommandFactory{
public:
    virtual IDBCommand* CreateDBCommand()=0;
};

class IDataReader{    
};
class IDataReaderFactory{
public:
    virtual IDataReader* CreateDataReader()=0;
};

//支持SQL Server
class SqlConnection: public IDBConnection{    
};
class SqlConnectionFactory:public IDBConnectionFactory{
};

class SqlCommand: public IDBCommand{    
};
class SqlCommandFactory:public IDBCommandFactory{
};

class SqlDataReader: public IDataReader{    
};
class SqlDataReaderFactory:public IDataReaderFactory{
};

//支持Oracle
class OracleConnection: public IDBConnection{
};

class OracleCommand: public IDBCommand{
};

class OracleDataReader: public IDataReader{
};

class EmployeeDAO{
    IDBConnectionFactory* dbConnectionFactory;
    IDBCommandFactory* dbCommandFactory;
    IDataReaderFactory* dataReaderFactory;
    
public:
    vector<EmployeeDO> GetEmployees(){
        IDBConnection* connection =
            dbConnectionFactory->CreateDBConnection();
        connection->ConnectionString("...");

        IDBCommand* command =
            dbCommandFactory->CreateDBCommand();
        command->CommandText("...");
        command->SetConnection(connection); //关联性

        IDBDataReader* reader = command->ExecuteReader(); //关联性
        while (reader->Read()){

        }
    }
};
```

但是，这样的问题是IDBConnection, IDBCommand, IDBDataReader可能并不对应，比如一个是oracel的connection, 一个是mysql的command。因此产生关联性问题。

* 继续重构

把connection, command, datareader凝聚到一个类中, 这样就能得到关联性的保证。

```cpp
//数据库访问有关的基类
class IDBConnection{    
};

class IDBCommand{
};

class IDataReader{    
};

class IDBFactory{
public:
    virtual IDBConnection* CreateDBConnection()=0;
    virtual IDBCommand* CreateDBCommand()=0;
    virtual IDataReader* CreateDataReader()=0;
    
};


//支持SQL Server
class SqlConnection: public IDBConnection{
};
class SqlCommand: public IDBCommand{
};
class SqlDataReader: public IDataReader{
};


class SqlDBFactory:public IDBFactory{
public:
    virtual IDBConnection* CreateDBConnection() {}
    virtual IDBCommand* CreateDBCommand() {}
    virtual IDataReader* CreateDataReader() {}
 
};

//支持Oracle
class OracleConnection: public IDBConnection{
};

class OracleCommand: public IDBCommand{
};

class OracleDataReader: public IDataReader{  
};

class OracleDBFactory:public IDBFactory{
public:
    virtual IDBConnection* CreateDBConnection() {}
    virtual IDBCommand* CreateDBCommand() {}
    virtual IDataReader* CreateDataReader() {}
 
};

class EmployeeDAO{
    IDBFactory* dbFactory;
    
public:
    vector<EmployeeDO> GetEmployees(){
        IDBConnection* connection =
            dbFactory->CreateDBConnection();
        connection->ConnectionString("...");

        IDBCommand* command =
            dbFactory->CreateDBCommand();
        command->CommandText("...");
        command->SetConnection(connection); //关联性

        IDBDataReader* reader = command->ExecuteReader(); //关联性
        while (reader->Read()){

        }

    }
};

```



## 结构

<img src="image-20200821143526358.png" alt="image-20200821143526358" style="zoom:50%;" />

## 总结

* 如果没有应对"多系列对象构建"的需求变化，则没有必要使用 Abstract Factory模式，这时候使用简单的工厂完全可以。

* "系列对象"指的是在某一特定系列下的对象之间有相互依赖、 或作用的关系。不同系列的对象之间不能相互依赖。

* Abstract Factory模式主要在于应对"新系列"的需求变动。其缺 点在于难以应对"新对象"的需求变动。