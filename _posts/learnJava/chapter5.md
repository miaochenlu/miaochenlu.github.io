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

### 5. 清理：终结处理和垃圾回收

Java有垃圾回收器负责回收无用对象占用的内存资源。

但是，假定对象(并非使用new)获得了一块特殊的内存区域，由于垃圾回收器只知道释放那些经由new分配的内存，所以它不知道如何释放该对象的这块特殊内存。

为了应对这种情况，Java允许在类中定义一个名为finalize()的方法。

他的工作原理假定是这样的：

一旦垃圾回收器准备好释放对象占用的存储空间，将首先调用其finalize()方法。并且在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。所以要是你打算用finalize(), 就能在垃圾回收时刻做一些重要的清理动作。

finalize()是Object中的方法，当垃圾回收器将要回收对象所占内存之前被调用，即当一个对象被虚拟机宣告死亡时会先调用它finalize()方法，让此对象处理它生前的最后事情（这个对象可以趁这个时机挣脱死亡的命运）。

#### 垃圾回收器如何工作

以前的语言在堆上分配对象的代价十分高昂，但是Java中的垃圾回收器提高了Java对象的创建速度-->什么？存储空间的释放会影响存储空间的分配hhh。这意味着：**Java从堆分配空间的速度，可以和其他语言从堆栈上分配空间的速度相媲美**。

* C++里的堆可以想象成一个院子。每个对象管理自己的地盘，一段时间后，对象可能被销毁，但是地盘必须加以重用
* Java虚拟机中，堆的实现像一个传送带，每分配一个新对象，他就往前移动一格。这意味着对象存储空间的分配速度非常快。Java的堆指针指示简单地移动到尚未分配的区域，其效率比得上C++在stack上分配空间的效率。

hh, 其实Java的堆未必像传送带一样工作，因为这样会导致频繁的页面换入换出。其实Java工作时，一面回收空间，一面使堆中的对象紧凑排列，这样堆指针可以很容易地移动到更靠近传送带到开始处，尽量避免了page fault.通过垃圾回收器将对象重新排列，实现了一种高速到、有无限空间可供分配的堆模型。

一些垃圾回收机制

* 引用计数：简单但是速度慢

> 每个对象含有一个引用计数器，当有引用连接到对象时，引用计数+1
>
> 当引用离开作用域或被置为null时，引用计数-1
>
>  
>
> 虽然管理引用计数的开销不大，但是这项开销在整个程序生命周期中将***持续发生***。
>
> 垃圾回收器会在含有全部对象的列表上遍历，当发现某个对象的引用计数为0时，就释放其占用的空间。
>
> 这种方法的一个缺陷时，如果对象之间存在循环引用，可能会出现对象应该被回收，但引用计数却不为0的情况。

* 对任何活的对象，一定能最终追溯到其存活在stack或静态存储区之中的引用。但是引用链条可能会穿过数个对象层次。因此，如果从stack和静态存储区开始，遍历所有的引用，就能找到所有活的对象。对于发现的每个引用，必须追踪它所引用的对象，然后是次对象包含的所有引用，如此反复进行，知道“根源于stack和静态存储区的引用"所形成的网络全部被访问为止。

在第二种方式下，Java虚拟机采用一种自适应的垃圾回收技术。如何处理找到的存活对象，取决于不同的Java虚拟机实现。

* 停止-复制(stop-and-copy): 

> 先暂停程序的运行（因此这种方式不属于后台回收模式），然后将所有存货的对象从当前堆复制到另一个堆，没有被复制的全部是垃圾。当对象被复制到新堆时，他们是一个挨着一个堆，所以新堆保持紧凑排列。
>
> 当把对象从一处搬到另一处时，所有指向它的那些引用必须修正。位于堆或者静态存储区的引用可以直接被修正，但可能还有其他指向这些对象的引用，他们在遍历的过程中被才能找到(可以想象成有个表格，将旧地址映射到新地址)
>
> 对于这种复制式回收器，效率会降低，有两个原因
>
> * 首先要有两个堆，在这两个分离的堆之间来回倒腾，从而得维护比实际需要多一倍多空间。某些Java虚拟机的处理方式时，按需从堆中分配几块较大的内存，复制动作发生在这些大块内存之间
> * 第二个问题是复制。程序进入稳定状态后，可能只会产生少量垃圾，甚至没有垃圾。但是复制式回收器会一直将内存从一处复制到另一处。对此问题，一些Java虚拟机会进行检查，要是没有新垃圾产生，就会转换到另一种工作模式(自适应)。这种模式称为标记-清扫。

标记-清扫:速度相当慢，但是当你知道只会产生少量垃圾甚至不会产生垃圾时，速度就很快

> 从stack和静态存储区出发，遍历所有的引用，今儿找出所有存货的对象。每当它找到一个存活对象，就会给对象设置一个标记，这个过程中不会回收任何对象。只有全部标记工作完成的时候，清理工作才会开始。
>
> 在清理过程中，没有标记的对象将会被释放，不会发生任何肤质动作，所以剩下的堆空间时不连续的，垃圾回收器要是希望得到连续空间的话，就得重新整理剩下的对象。

<br>

### 6. 成员初始化

Java 尽力保证：所有变量在使用前都能得到恰当的初始化。对于方法的局部变量，Java以编译时错误的形式来贯彻这种保证

```java
void f() {
  int i;
  i++;	//Error -- i not initialized
}
```

如果类的数据成员时基本类型，则会保证他们有一个初始值

在类里定义一个对象引用时，如果不将其初始化，此引用就会获得一个特殊值null.

### 7. 构造器初始化

可以用构造器来进行初始化。在运行时刻，可以调用方法活执行某些动作来确定初值。但是，无法阻止自动初始化的进行，它将在构造器被调用之前发生。

```java
public class Counter {
  int i;
  Counter() {i = 7;}
}
```

这里，首先i被置0，然后变成7.

#### A. 初始化顺序

在类的内部，变量定义的先后顺序决定了初始化的顺序。即使变量定义散布于方法定义之间，他们仍然会在任何方法（包括构造器）被调用之前得到初始化。

#### B. 静态数据的初始化

静态初始化只有在必要时刻才会进行。如果不创建对象或者不引用静态成员，那么静态数据也不会被创建。至于在第一个对象被创建（或者第一次访问静态数据）的时候，他们才会被初始化。此后，静态对象不会再被初始化。

初始化的顺序是：先静态对象，后为非静态对象

看一下整个过程

假设有个名为Dog的类

> 1. 即使没有显示使用static关键字，构造器实际上也是静态方法。当首次创建类型为Dog()的对象时(构造器可以视为静态方法)，或者Dog类的静态方法/静态域首次被访问时，Java解释器必须查找路径，定位Dog.class
> 2. 载入Dog.class, 有关静态初始化的所有动作都会执行。因此，静态初始化只在Class对象艘次加载时进行一次
> 3. 当用new Dog()创建对象的时候，首先将在堆上为Dog对象分配足够的存储空间
> 4. 这块存储空间会被清零，这就自动地将Dog对象中的所有基本数据类型都设置成了默认值，而引用被设置为null
> 5. 执行所有出现于字段定义处的初始化动作
> 6. 执行构造器

#### C. 显式的静态初始化

Java允许将多个静态初始化动作组织成一个特殊的静态块

```java
public class Spoon {
  static int i;
  static {
    i = 47;
  }
}
```

static后面的代码仅执行一次



#### D. 非静态实例初始化

​	类似上述方法来初始化每一个对象的非静态变量

```java
public class Mugs {
  Mug mug1;
  Mug mug2;
  {
    mug1 = new Mug(1);
    mug2 = new Mug(2);
  }
}
```



### 8. 数组初始化

```java
int[] a1;
```

编译器不允许指定数组的大小。现在拥有的只是对数组的一个引用，而且也没给数组对象本身分配任何空间。为了给数组创建相应的存储空间，必须写初始化表达式。对于数组，初始化动作可以出现在代码的任何地方

```java
int[] a1 = {1, 2, 3, 4, 5};
```

在Java中，可以将一个数组赋值给另一个数组。其实真正发生的只是赋值了一个引用

```java
int[] a2;
a2 = a1;
```



```java
public class ArraysOfPrimitives {
  public static void main(String[] args) {
    int[] a1 = {1, 2, 3, 4, 5};
    int[] a2;
    a2 = a1;
    for(int i = 0; i < a2.length(); i++)
      a2[i] = a2[i] + 1;
    for(int i = 0; i < a1.length(); i++)
      print(a1[i]);
  }
}
```

```
2
3
4
5
6
```



```java
int[] a = new int[rand.nextInt(20)];//数组大小由rand.nextInt随机决定
```

```java
Integer[] a = {
  new Integer(1),
  new Integer(2),
  3,
};
```



#### A. 可变参数列表

```java
public static void f(Integer...args) {
  for(Integer i : args)
    System.out.print(i + " ");
  System.out.println();
}
public static void main(String[] args) {
  f(new Integer(1), new Integer(2));
  f(3,5,7);
}
```

