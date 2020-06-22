---
layout: article
title: "Compilers--Semantics Analysis"
tag: Compiler
key: page-compiler_semantics_analysis
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
  background_image:
    src: https://miaochenlu.github.io/picture/image-20191208000900444.png.png


---

<!--more-->

<style>
  .page__header .header__brand path {
    fill: rgba(255, 255, 255, .95);
  }
</style>


<br/>



在一个典型的静态类型的语言 (如C语言)中， 语义分析包括

* 构造符号表
* 记录声明中建立的名字的含义
* 在表达式和语句中进行类型推断和类型检查
* 在语言的类型规则作用域内判断它们的正确性

语义分析可以分为两类，这两类不是相互排斥的

* 程序的分析

> 要求根据编程语言的规则建立其正确性， 并保证其正确执行
>
> LISP和Smalltalk这类动态制导的语言中，可能完全没有静态语义分析；而在 Ada这类语言中就 有很强的需求，程序必须提交执行

* 编译程序执行的分析

> 提高翻译程序执行的效率。通常包括对"最优化"或代码改进技术的讨论

# 1. Attributes and attribute grammars

## 1.1 Attributes

#### 哪些是Attributes？

* The ***data type*** of a variable
* The ***value*** of an expression
* The ***location*** of a variable in memory
* The ***object code*** of a procedure
* The ***numbers*** of significant digits in a number

#### 什么时候确定属性?

属性可以在编译过程中确定也可以在程序执行过程中确定

如: 一个数的有效位数可以在编译时确定; (非 常数)表达式的值， 或者动态分配的数据结构的位置在执行时确定

#### 什么时候binding?

绑定是计算属性的值并且将属性值绑定到属性名

Binding Time:  绑定发生的时间 compilation/execution

根据绑定时间可以将属性分类

* ***static attributes***: be bound prior to execution
* ***dynamic attributes***: be bound during exectuion

#### Type check

A type checker的工作

* computes the data type attribute of all language entities for which data types are defined(给一个变量a绑定了整数类型，我们判断给a的值是否是整数类型)
* verifies that these types conform to the ***type rule***s of the language. (比如int和double的数值运算全部转化为double计算)

<br>

{:.warning}

Type checking=set of rules that ensure the type consistency of different constructs in the program

<br>

一些rule的例子：

* The type of a variable must match the type from its  declaration

* The operands of arithmetic expressions (+, *, -,/)must have integer types; the result has integer type

* The operands of comparison expressions (==, !=)  must have integer or string types; the result has boolean type



## 1.2 Attribute grammars

### A. 定义

在***Syntax-directed semantics***(语法制导语义)中, 属性直接和语法符号关联绑定

如果`X`是一个文法符号,`a`是`X`的一个属性, 那么我们把与 X关联的a的值 记作`X.a`

<br>

给定属性$a_1,a_2,\cdots$, 对于一个语法规则$X_0\rightarrow X_1X_2\cdots X_n$($X_0$是非终结符,其他$X_i$是任意符号)，$X_i.a_j$的值和其他符号的属性值相关

因为这种相关，我们可以写出attribute equation

$$X_i.a_j=f_{ij}(X_0.a_1,\cdots,X_0.a_k,X_1.a_1,\cdots,X_1.a_k,\cdots,X_n.a_1,\cdots,X_n.a_k)$$

我们称这样的语法规则为为attribute grammar

<br>

### B. 形式

| Grammar Rule | Semantic Rules                 |
| ------------ | ------------------------------ |
| Rule 1       | Associated attribute equations |
| ...          | ...                            |
| Rule n       | Associated attribute equations |

<br>

***Example1：*** Given grammar rules

$number\rightarrow number\;digit\vert digit$

$digit\rightarrow 0\vert 1\vert 2\vert 3\vert 4\vert 5\vert 6\vert 7\vert 8\vert 9$

| Grammar Rule                        | Semantics Rules                            |
| ----------------------------------- | ------------------------------------------ |
| $number1\rightarrow number2\;digit$ | number1.val = number2.val * 10 + digit.val |
| $number\rightarrow digit$           | number.val = digit.val                     |
| $digit\rightarrow 0$                | digit.val=0                                |
| $digit\rightarrow 1$                | digit.val=1                                |
| $digit\rightarrow 2$                | digit.val=2                                |
| $digit\rightarrow 3$                | digit.val=3                                |
| $digit\rightarrow 4$                | digit.val=4                                |
| $digit\rightarrow 5$                | digit.val=5                                |
| $digit\rightarrow 6$                | digit.val=6                                |
| $digit\rightarrow 7$                | digit.val=7                                |
| $digit\rightarrow 8$                | digit.val=8                                |
| $digit\rightarrow 9$                | digit.val=9                                |

**注意第一行$number1\rightarrow number2\;digit$, 在这个 文法规则中对number 的两次出现必须进行区分，因为右边的 number 和左边的number的值不相 同因此改写为number1, number2**

give number 345

<img src="../../../assets/images/image-20200425192538263.png" alt="image-20200425192538263" style="zoom:30%;" />

## 1.3 Simplifications and Extensions to Attribute Grammars

### A. 一些概念

* 在属性等式中，原序出现的表达式的集合称为属性文法的元语言(metalanguage)

  * 算术式，逻辑式
  * if-then-else表达式，switch-case表达式
  * ***Functions***可以被加入到元语言中，必须在别处说明其作为属性文法的补充的定义

  $digit\rightarrow D$

  用numval函数digit.val=numval(D)



### B. 简化的方法

1. 使用原始文法二义性的但简单的形式(事实上，所有歧义都在parser阶段处理了)

<img src="../../../assets/images/image-20200425194213074.png" alt="image-20200425194213074" style="zoom:35%;" />

使用歧义文法后转化成

<img src="../../../assets/images/image-20200425194042640.png" alt="image-20200425194042640" style="zoom:40%;" />

2. 在显示属性值时，用抽象语法树来代替语法树

<img src="../../../assets/images/image-20200425194122636.png" alt="image-20200425194122636" style="zoom:40%;" />

使用两个辅助函数

* mkOpNode
  * 3个参数(构成一个树节点)
    * 一个操作符标记
    * 两个语法树
* mkNumNode(构成一个叶子节点)
  * 1个参数
  * 数字值

<img src="../../../assets/images/image-20200425194348302.png" alt="image-20200425194348302" style="zoom:40%;" />



# 2. Algorithms for attribute computation

## 2.1 Dependency Graphs and Evaluation Order

### A. dependency graph

给定一个属性文法，每个文法规则选择有一个相关依赖图 (associated dependency graph)。 文法规则中的每个符号在这个图中都有用每个属性 $X_i.a_j$ 标记的节点，对每个属性等式

$$X_i.a_j=f_{ij}(\cdots,X_m.a_k,\cdots)$$

相当于文法规则从在右边的每个节点$X_m.a_k$到节点$X_i.a_j$有一条边(表示$X_i.a_j$对$X_m.a_k$的依赖)



Example:

$decl\longrightarrow type\; var-list$

$type\longrightarrow int\vert float$

$var-list\longrightarrow id,var-list\vert id$

<br>

* 文法规则$var-list\longrightarrow id,var-list\vert id$对应两个属性等式

&emsp;$id.dtype = var-list_1 .dtype$

&emsp;$var-list_2 .dtype = var-list_1.dtype$

&emsp;画出依赖图

<img src="../../../assets/images/image-20200508155649508.png" alt="image-20200508155649508" style="zoom:40%;" /><img src="../../../assets/images/image-20200508155702892.png" alt="image-20200508155702892" style="zoom:40%;" />

要注意的是：在画语法树节点时我们不使用属性的圆点符号，而是通过写出与其相连的下一个节点来表示属性。这样，在这个例子中第一个相关 也可以画作

<img src="../../../assets/images/image-20200508160044295.png" alt="image-20200508160044295" style="zoom:40%;" />

* $type\longrightarrow int\vert float$无关紧要
* $decl\longrightarrow type\; var-list$

&emsp;$var-list.dtype=type.dtype$

&emsp;画出依赖图

<img src="../../../assets/images/image-20200508155836697.png" alt="image-20200508155836697" style="zoom:40%;" />

&emsp;在这种情况中，因为decl不直接包含在相关图中，所以这个相关图相关的文法规则并不完全清晰。我们通常**重叠在相应的文法规则的语法树片段上绘制相关图**。这样，上面的相关图就可以画作

<img src="../../../assets/images/image-20200508155926030.png" alt="image-20200508155926030" style="zoom:40%;" />

`float x,y`的相关图是

<img src="../../../assets/images/image-20200508160159987.png" alt="image-20200508160159987" style="zoom:40%;" />

### B. Directed acyclic graphs(DAG)

算法必须计算出dependency graph中每个node的属性值后才能计算后续属性。因此可以用拓扑排序得到一个计算顺序，拓扑排序要求图无环

给节点编号

<img src="../../../assets/images/image-20200508160448119.png" alt="image-20200508160448119" style="zoom:40%;" />

<br>

### Comments

* Parse tree method:  

  这种方法存在问题

  * 在编译时构造相关图增加了额外的复杂性
  * 这种方法在编译时确定相关图是否非循环时，它通常不恰当地等待发现一个环，直到到达编译时间，因为在原始的属性文法中， 环几乎都是表示错误。 换句话说， 属性文法必须预先进行无环测试。有一个算法进行这个工作，但这是一个时间***指数级增长***的算法。

* Rule based method: fix an order for attribute evaluation at compiler construction time. It depends on an analysis of the attribute equations, or semantics rules.



## 2.2 Synthesized and inherited attributes

* #### ***synthesized attributes***

在parse tree中从孩子指向父亲的属性是synthesized attributes. 在分析树节点N上的非终结符A的综合属性是由N上的产生式所关联的语义规则来定义的。这个产生式的头一定是A。<u>节点N上的综合属性只能通过N的子节点或N本身的属性值来定义</u>

给定语法规则$A\rightarrow X_1X_2\cdots X_n$, the only associated attribute equation with an `a` on the left-hand side is of the form

$A.a=f(X_a.a_1,\cdots,X_1.a_k,\cdots,X_n.a_1,\cdots,X_n.a_k)$

我们称一个文法为S-attributed grammar, 如果所有属性都是synthesized attributes

***计算***：

可以通过bottom-up, post-order遍历的方式计算

```pascal
procedure postEval(T:treenode);
begin
	for each child C of T do
	postEval(C);
	compute all synthesized attributes of T;
end
```

* #### ***inherited attributes***

在分析树节点N上的非终结符B的继承属性是由N的父节点上的产生式所关联的语义规则来定义的。这个产生式的体中必含符号B。<u>节点N上的综合属性只能通过N的父节点, N本身和N的兄弟节点上的属性值来定义</u>

<img src="../../../assets/images/image-20200425234201883.png" alt="image-20200425234201883" style="zoom:25%;" />

***计算***：

可以通过preorder, combined preorder/inorder 遍历的方式计算

```pascal
procedure preEval(T:treenode)
begin
	for each child C of T do
		computed all inherited attributes of C
		preEval(C)
end
```

Remark:

* The order in which the inherited attributes of the children are computed is important.

* It must adhere to any requirements of the dependencies

<br>

#### Example:

<img src="../../../assets/images/image-20200508160159987.png" alt="image-20200508160159987" style="zoom:40%;" />

代码中混合了前序和中序操作

* Inorder: decl node
* Preorder: var-list node

```pascal
procedure evalType(T:treenode);
begin
	case nodekind of T of //根据node的类型
	decl:
				//判断type.dtype
				evalType(type child of T)	
				//将type.dtype赋值给var-list
				assign dtype of type child of T to var-list child of T
				//为var-list的dtype赋值
				evalType(var-list child of T)
	type: 
				if child of T = int then T.dtype := integer
				else T.dtype := real;
	var-list:
				//将var-list.dtype赋值给第一个儿子
				assign T.dtype to first child of T;
				//如果有第三个儿子，继续赋值
				if third child of T is not nil then
							assign T.dtype to third child;
							evalType(third child of T);
	end case;
end EvalType;
```



<img src="../../../assets/images/image-20200508175426749.png" alt="image-20200508175426749" style="zoom:20%;" />

```cpp
typedef enum {decl, type, id} nodekind;
typedef enum {integer, real} typekind;
typedef struct treenode {
  nodekind kind;
  //sibling是给var-list用的
  struct treenode *lchild, *rchild, *sibling;
  typekind dtype;
  char* name;
}*Syntaxtree;

void evaltype(syntaxtree t) {
  switch(t->kind) {
    case decl:
      t->rchild->dtype = t->lchild->dtype;
      evaltype(t->rchild);
      break;
    case id:
      if(t->sibling != NULL) {
        t->sibling->dtype = t->dtype;
        evaltype(t->sibling);
      }
      break;
  }
}
```



## 2.3 Attributes as parameters and returned values

* Inherited attributes: 经常是通过preorder遍历计算，所以将其作为调用的参数(从上往下传)
* synthesized attributes: 经常通过postorder遍历计算，所以将其作为return values(从下往上传)

## 2.4 使用扩展数据结构存储属性值

对那些不能方便地把属性值作为参数或返回值使用的情况 (特别是当属性值有重要的结构时，在翻译时可能专门需要 )， 把属性值存储在语法树节点也是不合理的。 在这些情况中， 如查表、图以及其他一些数据结构对于获得属性值的正确活动和可用性都是有用的。

## 2.5 语法分析时属性的计算

提出问题：在分析阶段的同时要计算哪种扩展属性，而无须等到通过语法树的递归遍历进行对源代码的多遍处理?

在一次分析中哪些属性能成功地计算在很大程度上要取决于使用分析方法的能力和性质。 这样一个重要的限制是所有主要的分析方法都***从左向右处理输入程序*** (这也是前面两章研究LL 和LR分析技术的第1个L的内容)。它等价于要求属性能通过从左向右遍历分析树进行赋值。

对于合成属性这不是一个限制，因为节点的子节点可以用任意顺序赋值，特别是从左向右。但是对于继承属性，这就意味着在相关图中没有“向后”的依赖 (在分析树中依赖从右指向左 )。

`definition`{:.error}

定义$a_1,\cdots,a_k$的一个属性文法是**L-attributed**, 如果对每个继承属性$a_j$和每个文法规则

$$X_0\longrightarrow X_1X_2\cdots X_n$$

$a_j$的相关等式都有以下形式

$X_i.a_j=f_{ij}(X_0.a_1,\cdots,X_0.a_k,X_1.a_1,\cdots,X_1.a_k,\cdots,X_{i-1}.a_1,\cdots,X_{i-1}.a_k)$

在$X_i$处$a_j$的值只依赖于在文法规则中$X_i$左边出现的符号$X_0,\cdots,X_{i-1}$的属性

<br>

给定一个L-属性文法， 其继承属性不依赖于合成属性， 如前所述， 通过把继承属性转换成参数以及把合成属性转换成返回值，递归下降的分析程序可以对所有的属性赋值。

然而， LR分析程序，如Yacc产生的LALR(1)分析程序，适合于处理主要的**合成属性**。反过来说，其原因在于LR分析程序的功能强于 LL分析程序。当已知在一个派生中使用的文法规则时，属性才变成可计算的，这是因为只有在那时才能确定属性计算的等式。但是， LR分析程序将确定在派生中使用的文法规则推迟到文法规则的右部完全形成时才使用。这使得使用继承属性十分困难，除非它们的特性对于所有可能的右部选择都是固定的。

<br>

* LR分析中合成属性的计算: LR分析程序中,通常由一个value stack存储合成属性。value stack将和parsing stack并行操作，根据属性等式每次在分析栈出现shift或reduce来计算新值。

<img src="../../../assets/images/image-20200508224900790.png" alt="image-20200508224900790" style="zoom:35%;" />

* Inheriting a previously computed synthesized attributes during LR parsing

比如$A\longrightarrow B\;C$ 

$C.i=f(B.s)$ 其中s是合成属性

处理方法是在B,C中间插入空产生式D, 处理D时暂存B的属性值

<img src="../../../assets/images/image-20200508225812526.png" alt="image-20200508225812526" style="zoom:30%;" />

在Yacc中处理起来就容易了, 直接 

```flex
A:B{saved_i=f($1);}C;
```

这里伪变量$1指的是B的值，动作执行时它在栈顶)

* 如果我们能够预测以前计算的合成属性在什么位置的话，可以在value stack中直接访问来解决一些继承属性的计算问题

<img src="../../../assets/images/image-20200508230350269.png" alt="image-20200508230350269" style="zoom:35%;" />



### 2.6 The Dependence of Attribute Computation on the Syntax

> Knuth: Given an attribute grammar, **all inherited attributes can be changed into synthesized attributes** by suitable modification of the grammar, without changing the language of the grammar.



# 3. The symbol table

为什么要有符号表：这是一个外部存储结构，存储了符号的相关信息

符号表包含的内容

* the name of an identifier
* additional information: kind, type, constant

| NAME | KIND | TYPE               | ATTRIBUTES |
| ---- | ---- | ------------------ | ---------- |
| foo  | fun  | int x int --> bool | extern     |
| m    | arg  | int                |            |
| n    | arg  | int                | const      |
| tmp  | var  | bool               | const      |

## 3.1 The structure of the symbol table

符号表的作用是将标识符映射到他们的类型和存储位置。在处理类型，变量和函数的声明时，这些标识符便与其在符号表中的含义相绑定。每当发现标识符的使用时，便在符号表中查看他们的含义。

符号表的主要操作有：插入，查找，删除

符号表可以有多种实现方式

### A. Linear List

* insert: constant time
* look: linear time
* delete: linear time

线性表实现简单，但是效率不高



### B. Various Search Tree Structures

binary search tree, AVL tree, B tree

这种实现效率不是最高的并且删除操作非常复杂



### C. Hash Tables

insert, look, delete几乎都可以在常数时间完成

The size of the hash table: 几百到1000， 并且bucket array的长度应该选择一个素数



#### The process of the hash function

1. Converts a character string(the identifier name) into a nonnegative integer.
2. These integers are combined in someway to form a single integer
3. The result integer is scaled to the range 0,... ,size-1

<br>

#### The algorithm of the hash function

repeatedly use a constant number $\alpha$ as a multiplying factor when adding in the value of the next character.

$h_{i+1}=\alpha h_i+c_i,h_0=0$

final hash value $h=h_n\mod{size}$

n是这个string中字符的数量

$h=(\alpha^{n-1}c_1+\alpha^{n-2}c_2+\cdots+\alpha c_{n-1}+c_n)\mod {size}$

$\alpha$的选择会影响效率，一般选择$\alpha$为2的幂次，这样的话可以用移位实现乘法，效率更高。

#### Deal with collisions

* open addressing 
* **separate chaining**

<img src="../../../assets/images/image-20200509225410307.png" alt="image-20200509225410307" style="zoom:40%;" />

## 3.2 Declarations

分类

* 常量声明
* 类型声明
* 变量声明
* procedure/function声明

### A. Constant Declarations

Value binding: associate values to names

The values that ca be bound determine how the compiler treats them

* Pascal, Modula-2: the values in a constant declaration be **static**, computable by the compiler
* C, Ada: permit constants to be **dynamic**, only computable during execution

### B. Type Declarations

Bind names to newly constructed types and may also create aliases for existing named types

Type names are used in conjunction with a type equivalence algorithm(perform type checking of a program)

### C. Variable Declarations

Bind names to data types

变量声明时可能绑定了其他隐藏属性，比如变量作用域

### D. Procedure/Function Declarations

### E. The strategies

* 用一张symbol table来存储所有类型的声明
* 用不同的symbol table来存储不同类型的声明
* symbol table对应程序的不同区域，并且将这些symbol table根据文法规则连接起来

## 3.3 Scope rules and block structure

two rules:

* Declarations before use
* The most closely nested rule for block structure

### A. Declaration Before Use

* 符号表在parsing过程中建立

* lookups to be performed as soon as a name reference is encountered in the code

  * If the lookup fails
    * a violation of declaration before use has occurred
    * the compiler will issue an appropriate error message

  ### B. The most closely nested rule for block structure

  Block: any construct that can contain declarations, such as procedure/function declarations.

  

  * Pascal
    * the blocks are the main program
    * procedure/function declarations
* C
    * the blocks are the compilation units, procedure/function declarations.
    * the compound statements
    
    
  
  一种语言是block structured，如果它允许在其他块的内部嵌入块， 并且如果一个块中说明的作用域限制在本块以及包含在本块的其他块中，服从最近嵌套规则 (most closely nested rule)：为同一个名字给定几个不同的说明，被引用的说明是最接近引用的那个嵌套块。

```cpp
int i, j;
int f(int size) 
{
    char i, temp; ...
  {
    double j; ...
  }
  {
    char* j; ...
  }
}
```

为了实现嵌套作用域和最近嵌套规则

* 符号表插入操作不必改写前面的说明，但必须临时隐藏它们，这样查找操作只能找到名字最近插入的说明。
* 删除操作不应删除与这个名字相应的所有说明，只需删除最近的一个，而显示前面任何的说明。

符号表在处理嵌套作用域期间的行为类似于stack的方式

* 进入f函数后

<img src="../../../assets/images/image-20200609234914602.png" alt="image-20200609234914602" style="zoom: 40%;" />

* 处理到第二个嵌套block

<img src="../../../assets/images/image-20200609235247226.png" alt="image-20200609235247226" style="zoom:40%;" />

* 退出函数f

<img src="../../../assets/images/image-20200609235319244.png" alt="image-20200609235319244" style="zoom:40%;" />

但是也可以每个作用域维护独立的符号表，从内到外链接在一起，这样如果查找操作在当前表中没有找到名字，就自动用附上的表继续搜索

<img src="../../../assets/images/image-20200609235539060.png" alt="image-20200609235539060" style="zoom:40%;" />

## 3.4 Interation of same-level declarations

***在同一 层中不能使用名字相同的declarations。***

比如下面的例子引发error

```cpp
typedef int i;
int i;
```

为检查这个要求，在每次插入前编译器必须执行一次查找，通过某种机制 (如嵌套层)确定在同 一层中任何已存在的说明是否有相同的名字

***在相同层的序列中名字相互之间有多少可用的信息***

```cpp
int i = 1;
void f() {
    int i = 2, j = i + 1;
}
```

f内部`j`的值是初始化成 2还是3，即使用的是i的局部declaration还是非局部declaration。 

根据最近嵌套规则，应该是使用最近的declaration—局部declaration

* sequential declaration: each declaration is added to the symbol table as it is processed
* Collateral[并列] declaration: 
  * declarations not be added immediately to the existing symbol table
  * accumulated in a new table(or temporary structure)
  * added to the existing table after all declarations have been processed.
* Recursive declaration
  * declaration may refer to themselves or each other

# 4. 数据类型和类型检查

两大任务: 

* Type inference
* Type checking

**Type checking**: 

> set of rules that ensure the type consistency of different constructs in the program.

## 4.1 类型表达式和类型构造器

### A. Simple types

`int`, `double`, `boolean`, `char`

> the values exhibit no explicit internal structure, and the ttypical representation is also simple and predefined.

`void`: has no value, represent the empty set.

new simple type defined sush as **subrange** types and **enumerated** types.

<br>

### B. Structured type

New data types cana be created using typed constructors.

Such constructors can be viewed as functions:

* take existing types as parameters
* return new types with a structure that depends on the constructor

<br>

#### Array

**Type parameter:**

* index type
* component type

Arrays are commonly allocated conriguous storage from smaller to larger indexes.

Allow for the use of automatic offset calculations during execution.

The amount of emory needed is $n\times size$

<br>

#### Record

A record or structure type constructor takes a list of names and associated types and constructs a new type

```cpp
struct {
    double r;
    int i;
};
```

Different types may be combined.

The names are used to access the different components.

<img src="../../../assets/images/image-20200613190805725.png" alt="image-20200613190805725" style="zoom:30%;" />

#### Union

correspond to the set union operation

```cpp
union {
    double r;
    int i;
};
```

Disjoint union, each value is viewed as either real or an integer, but never both.

如果x是给定的union类型的一个变量，那么`x.r`表示`x`是实数时的值，而`x.i`表示`x`是整数时的值。

Allocate memory in parallel for each component.

<img src="../../../assets/images/image-20200613191045367.png" alt="image-20200613191045367" style="zoom:30%;" />



#### Pointer

Values that are references to values of another type. Most useful in describing recursive type.

A value of a pointer type is a memory address whose location holds a value of its base type.

#### Function

```pascal
VAR f: PROCEDURE (INTEGER) : INTEGER
```

这说明`f`时函数(或者过程)类型，带有一个整数参数，并产生一个整数结果。

#### 类

## 4.2 类型名，类型说明，递归类型

Type declarations (type definition)提供了一个给类型表达式赋名的机制

Such as: `typedef`, `=`

```cpp
typedef struct {
    double r;
    int i;
} RealIntRec;
```

type definition使类型名进入符号表。类型名不能重用为变量名(作用域嵌套规则允许除外)。

<br>

Since type names can appear in type expressions, questions, arise about the recursive use of type names.

Such recursive data types are extremely important in modern programming languages include lists, trees, and many other structures

```cpp
//indirect use of recursion (ML language)
struct intBST {
    int val;
    struct intBST* left, *right;
};
typedef struct intBST* intBST;
```

## 4.3 Type equivalence

**Type equivalence:** two types expression represent the same type.

<br>

```pascal
record 
	x: pointer to real;
	y: array[10] of int;
end
```

<img src="../../../assets/images/image-20200613200828267.png" alt="image-20200613200828267" style="zoom:25%;" />

```pascal
proc(bool, union a: real, b: char end, int): void
```

<img src="../../../assets/images/image-20200613200942682.png" alt="image-20200613200942682" style="zoom:25%;" />

classification of type equivalence: 

* structural equivalence
* name equivalence
* declaration equivalence

#### Structural equivalence(最弱的)

* Two types are the same if and only if they have the same structure (they have syntax trees that are identical in structure)

两个数组时不等价的，除非他们有相同的大小和元素类型

两个记录时不等价的，除非他们有相同的元素并且元素有相同的名字和顺序。



#### Name equivalence(最强的)

* Restricted variable declarations and type subexpressions to **simple types** and **type names**.

```cpp
t1 = int;
t2 = int;
//t1 and t2 are not equivalent, type names are different
```

判断代码

```pascal
function typeEqual(t1, t2: TypeExp): Boolean;
var temp : Boolean;
	p1, p2 : TypeExp;
begin
	if t1 and t2 are of simple type then
		return t1 = t2;
	else if t1 and t2 are type names then
		return t1 = t2;
	else return false;
end;
```

#### Declaration equivalence

```cpp
t2 = t1
```

对于上述说明，用类型别名(aliase)解释，而不是新的类型。

因此

```cpp
t1 = int;
t2 = int;
```

t1和t2对int等价

**every type name is equivalent to some base type name, which is either a predefined type or is given by a type expression resulting from the application of a type constructor.**

```cpp
t1 = array[10] of int;
t2 = array[10] of int;
t3 = t1;
```

t1和t3是等价的，但是和t2不等价



* Pascal 一律使用declaration equivalence
* C对struct和union使用declaration equivalence, 对指针和数组使用structural equivalence

## 4.4 Type inference and type checking

```cpp
if not typeEqual(exp.type, boolean)
then type-error(stmt)
```





























