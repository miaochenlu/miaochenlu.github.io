# 1. What is data type

When you declare that a variable has a certain type, you are saying that 

(1) the values that the variable can have are elements of a <u>certain set</u> and 

(2) there is a collection of <u>operations</u> that can be applied to those values



# 2. Various Data Types

## 2.1 Primitive Data Types

## 2.2 Character String Types

Design issues:

> * Is it a primitive type or just a special kind of array? 
> * Should the length of strings be static or dynamic? 
> * What kinds of string operations are allowed?
>   * Assignment and copying: what if have diff. lengths? 
>   * Comparison (=, >, etc.) 
>   * Catenation
>   * Substring reference: reference to a substring 
>   * Pattern matching

## 2.3 Pointer

### A. Dangling Pointer Problem

对象的3种存储类别：静态、栈和堆。

* 静态对象在程序的执行期间始终是活动的
* 栈对象在它们的声明所在的子程序执行期间是活动的
* 堆对象则没有明确定义活动时间。

在对象不在活动时，长时间运行的程序就需要回收该对象的空间，栈对象的回收将作为子程序调用序列的一部分被自动执行。

而在堆中的对象，由程序员或者语言的自动回收机制负责创建或者释放，那么如果一个活动的指针并没有引用合法的活动对象，这种情况就是悬空引用。比如程序员显示的释放了仍有指针引用着的对象，就会造成悬空指针，再进一步假设，这个悬空指针原来指向的位置被其他的数据存放进去了，但是实际却不是这个悬空指针该指向的数据，如果对此存储位置的数据进行操作，就会破坏正常的程序数据。

那么如何从语言层面应对这种问题呢？Algol 68的做法是禁止任何指针指向生存周期短于这个指针本身的对象，不幸的是这条规则很难贯彻执行。因为由于指针和被指对象都可能作为子程序的参数传递，只有在所有引用参数都带有隐含的生存周期信息的情况下，才有可能动态的去执行这种规则的检查。

### B. garbage collection

对程序员而已，显示释放堆对象是很沉重的负担，也是程序出错的主要根源之一，为了追踪对象的生存轨迹所需的代码，会导致程序更难设计、实现，也更难维护。一种很有吸引力的方案就是让语言在实现层面去处理这个问题。随着时间的推移，自动废料收集回收都快成了大多数新生语言的标配了，虽然它的有很高的代价，但也消除了去检查悬空引用的必要性了。关于这方面的争执集中在两方：以方便和安全为主的一方，以性能为主的另一方。

废料收集一般有这两种思想.

- 引用计算

> A counter in every variable, storing number of pointers currently pointing at that variable.
>
> If counter becomes zero, variable becomes garbage and can be reclaimed 
>
> 
>
> Disadvantages: space required, execution time required, complications for cells connected circularly

- 追溯式收集

> Every heap cell has a bit used by collection algorithm 
>
> All cells initially set to garbage 
>
> All pointers traced into heap, and reachable cells marked as not garbage " All garbage cells returned to list of available cells 
>
> 
>
> Disadvantages: when you need it most, it works worst (takes most time when program needs most of cells in heap)

# 3. Type Binding

Before a variable can be referenced in a program, it must be bound to a data type 

* How is a type speciﬁed? 
*  When does the binding take place?



If static, the type may be speciﬁed by either 

* Explicit declaration: by using declaration statements 
* Implicit declaration: by a default mechanism, e.g., the ﬁrst appearance of the variable in the program 

> Fortran, PL/I, BASIC, Perl have implicit declarations
>
> * Advantage: writability
> * Disadvantage: reliability (less trouble with Perl)



## 2.1 When to bind

### A. Dynamic Type Binding

> A variable is assigned a type when it is assigned a value in an assignment statement and is given the type of RHS

```php
list = [2, 4.33, 6, 8];
list = 17.3
```

`优点`{:.success}

> ﬂexibility (generic for processing data of any type, esp. any type of input data)

`缺点`{:.error}

> * <u>High cost</u> (dynamic type checking and interpretation) 
>
> * <u>Less readable</u>, difﬁcult to detect type error by compiler 
>
> PL usually implemented in interpreters



## 2.2 How to specify

### A. Type Inference

> Types of expressions may be inferred from the context of the reference

```haskell
fun square(x) = x * x;
```

Arithmetic operator * sets function and parameters to be numeric, and by default to be int

```cpp
square(2.75); //error!
fun square(x) : real = x * x; //correct
```



# 4. Type Checking

> The activity of ensuring that the operands of an operator are of compatible types

A compatible type is one that is either legal for the operator, or is allowed to be implicitly converted, by compiler-generated code, to a legal type, e.g.,  

```cpp
(int) A =(int) B + (real) C
```

This automatic conversion is called a ***<u>coercion</u>***



All type bindings static--->nearly all type checking can be static  

Type binding dynamic ---> type checking dynamic







> 在程序中定义变量类型对编译器有什么好处？对解释语言，这种好处还存在吗？为什么？

1. 

> 为许多操作提供了隐含的上下文信息，使程序员可以在许多情况下不必显示的描述这种上下文。比如int类型的两个对象相加就是整数相加、两个字符串类型的对象相加就是拼接字符串、在Java和C#中new object()隐含在背后的就是要分配内存返回对象的引用等等。
>
> 类型描述了其对象上一些合法的可以执行的操作集合。类型系统将不允许程序员去做一个字符和一个记录的加法。<u>编译器可以使用这个合法的集合进行错误检查，好的类型系统能够在实践中捕获很多错误</u>
>
> <a href="https://www.cnblogs.com/secoding/p/11944773.html">excellent article</a>

2. 

程序效率
动态类型不利于编译优化。 

如JavaScript中，运算中操作数的类型不会静态限定。在解释或编译产生的代码动态执行中，类型的判断、转换会提高复杂度。

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200115104227994.png" alt="image-20200115104227994" style="zoom:50%;" />





# Side effects in Expression

> Functional side effects: a function changes a two-way parameter or a non-local variable

A side effect is something that creates a change outside of the currrent code scope, or something external that affects the behaviour or result of executing the code in the current scope.

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200113000656517.png" alt="image-20200113000656517" style="zoom:40%;" />













