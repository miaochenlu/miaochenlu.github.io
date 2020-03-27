# 初始化与清理

## 1. 用构造器确保初始化

```java
class Rock {
  Rock() {	// This is the constructor
    System.out.print("Rock ");
  }
}
public class SimpleConstructor {
  public static void main(String[] args) {
    for(int i = 0; i < 10; i++)
      new Rock();
  }
}
```

在创建对象时:new Rock();

将会为对象分配存储控价呢，并调用相应的构造器。这就确保了在能操作对象之前，它已经被恰当地初始化了

* 不接受任何参数的构造器叫做默认构造器
* 构造器也可以带有形式参数，以便指定如何创建对象

<br>

## 2. 方法重载

```java
import static net.mindview.util.Print.*;
class Tree{
  int height;
  Tree() {
    print("Planting a seeding");
    height = 0;
  }
  Tree(int initialHeight) {
    height = initialHeight;
    print("Creating new Tree that is " + height + " feet tall");
  }
  
  void info() {
    print("Tree is " + height + " feet tall");
  }
  void info(String s) {
    print(s + ": Tree is ") + height + " feet tall";
  }
}
public class Overloading {
  public static void main(String[] args) {
    for(int i = 0; i < 5; i++) {
      Tree t = new Tree(i);
      t.info();
      t.info("Overloaded method");
    }
    new Tree();
  }
}
```

### A. 区分重载方法

每个重载的方法都必须有一个独一无二的参数类型列表

### B. 涉及基本类型的重载

基本类型能从一个较小的类型自动提升到一个较大的类型，此过程一旦牵涉到重载，可能会造成一些混淆。

如果传入的数据类型(实际参数类型)小于方法中声明的形式参数类型，实际数据类型就会被提升

如果传入的实际参数大于重载方法声明的形式参数，就得通过类型转换来执行窄化操作

### C. 为什么不能以返回值类型区分重载方法

```java
void f() {}
int f() {return 1;}
```

在int x = f()中，的确可以根据这条语句来区分重载方法。但是如果这样调用

```java
f();
```

这时java就没有办法判断该调用哪一个f()了



## 3. 默认构造器

默认构造器是没有形式参数的，他的作用是创建一个默认对象。如果你写的类中没有构造器，那么编译器会自动帮你创建一个默认构造器

```java
class Bird {}
public class DefaultConstructor {
  public static void main(String[] args) {
    Bird b = new Bird(); 	//Default!
  }
}
```

new Bird()行创建了一个新对象，并且调用了他的默认构造器。

{:.warning}

如果已经定义了一个构造器(无论是否有参数)，编译器就不会帮你自动创建默认构造器

```java
class Bird2 {
  Bird2(int i) {}
  Bird2(double d) {}
}

public class NoSynthesis {
  public static void main(String[] args) {
    //! Bird2 b = new Bird2();//error, No default
    Bird2 b2 = new Bird2(1);
    Bird2 b3 = new Bird3(1.0);
  }
}
```

### 4. this关键字

如果有同一类型的两个对象a,b， 怎么才能让这两个对象都能调用peel()方法呢？

```java
class Banana {
  void peel(int i) {}
}

public class BananaPeel {
  public static void main(String[] args) {
    Banana a = new Banana();
    Banana b = new Banana();
    a.peel(1);
    b.peel(1);
  }
}
```

如果只有一个peel()方法，它如何知道是被a还是被b调用的

编译器做了一些幕后工作，他暗自吧"所操作对象的引用"作为第一鹅参数传递给peel()。上述两个方法的调用就变成了这样

```java
Banana.peel(a, 1);
Banana.peel(b, 2);
```

假设你希望在方法的内部获得对当前兑现对引用。由于这个引用是有编译器偷偷传入的，所以没有标识符可以用。但是，为此又个专门的关键字: this。this只能在方法内部使用，表示对调用方法对那个对象的引用。

#### I. 在构造器中调用构造器

可能为一个类写了多个构造器，有时可能像在一个构造器中调用另一个构造器，以避免重复代码，可以用this

```java
public class Flower {
  int petalCount = 0;
  String s = "initial value";
  Flower(int petals) {
    petalCount = petals;
    print("Constructor w/ int arg only, petalCOunt = " + petalCount);
  }
  Flower(String ss) {
    print("Constructor w/ String arg only, s = " + ss);
    s = ss;
  }
  Flower(String s, int petals) {
/*
* 在构造器中调用构造器
*/
    this(petals);
	  //this(s) //Can't call two
    this.s = s;
    print("String & int args");
  }
  Flower() {
    this("hi", 47);
    print("default constructor (no args)");
  }
}
```

#### II. static的含义

static方法就是没有this的方法。在static方法的内部不能调用非静态方法。

可以在没有创建任何对象的前提下，仅仅通过类本身来调用static方法，这实际上正是static方法的主要用途