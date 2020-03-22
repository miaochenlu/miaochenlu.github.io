# 操作符

## 1. 赋值

对一个对象进行操作时，我们真正操作的是对对象的引用。将一个对象赋值给另一个对象，实际是将引用从一个地方赋值到另一个地方。

```java
import java.util.*;
class Tank {
  int level;
}

public class Assignment {
  public static void main(String[] args) {
    Tank t1 = new Tank();
    Tank t2 = new Tank();

    t1.level = 9;
    t2.level = 47;
    t1 = t2;
    System.out.println("t1.level: " + t1.level + ". t2.level: " + t2.level);
    t1.level = 27;
    System.out.println("t1.level: " + t1.level + ". t2.level: " + t2.level);
  }
}
```

result

```
t1.level: 47. t2.level: 47
t1.level: 27. t2.level: 27
```

将t2赋值给t1,接着又修改了t1。由于赋值操作的是同一个对象的引用，所以修改t1的同时也改变了t2。因为t1,t2包含的是相同的引用，它们指向相同的对象。

{:.warning}

这种现象叫做别名现象



```java
class Letter {
  char c;
}
public class PassObject {
  static void f(Letter y) {
    y.c = 'z';
  }
  public static void main(String[] args) {
    Letter x = new Letter();
    x.c = 'a';
    System.out.println("x.c: " + x.c);
    f(x);
    System.out.println("x.c: " + x.c);
  }
}
```

result

```
x.c: a
x.c: z
```

在许多编程语言中，方法f()要在它的作用域内赋值其参数Letter y的一个副本，但实际上只是传递了一个引用

## 2 . 随机数

首先创建一个Random类对象，如果在创建过程中没有传递任何参数，那么java就会将当前时间作为随机数生成器的种子，并由此在程序每一次执行时都能产生不同的输出。

但是，通过在创建Random对象时提供种子(用于随机数生成器的初始化值，***随机数生成器对于特定的种子值总是产生相同的随机数序列***)，就可以在每一次执行程序时产生相同的随机数，因此其输出是可以验证的。

通过Random类的对象，程序可以产生许多不同类型的随机数字。传递给nextInt()的参数设置了产生的随机数的上限，而其下限为0

```java
Random rand = new Rand(47);
int j = rand.nextInt(100) + 1;
double v = rand.nextFloat();
double w = randnextDouble();
```











## 3. 关系操作符

### A. 测试对象的等价性

```java
public class Equivalence {
  public static void main(String[] args) {
    Integer n1 = new Integer(47);
    Integer n2 = new Integer(47);
    System.out.println(n1 == n2);
    System.out.println(n1 != n2);
  }
}
```

result

```
false
true
```

{:.error}

两个Integer对象的内容相同，但是对象的引用是不同的。==和=!比较的是对象的引用。如果要比较两个对象的内容是否相同，需要用equals()方法。基本类型直接使用==和！=即可

```java
public class Equivalence {
  public static void main(String[] args) {
    Integer n1 = new Integer(47);
    Integer n2 = new Integer(47);
    System.out.println(n1.equals(n2));
  }
}
```

result

```
true
```

然而，事情并非如此简单

```java
class Value {
  int i;
}
public class EqualsMethod2 {
  public static void main(String[] args) {
    Value v1 = new Value();
    Value v2 = new Value();
    v1.i = v2.i = 100;
    System.out.println(v1. equals(v2));
  }
}
```

result

```
false
```

为什么又是false呢？因为equals()默认比较引用是否相同。因此，如果要比较内容的话，需要自己在新类中覆盖equals()方法。





