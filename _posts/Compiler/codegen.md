# 代码生成

<img src="../../assets/images/image-20200601192951566.png" alt="image-20200601192951566" style="zoom:50%;" />

# 1. 中间代码和用于中间代码生成的数据结构

在翻译期间，intermediate representation(IR)代表了源程序的数据结构。

一般使用抽象语法树作为主要的IR

除了IR外，翻译期间的主要数据结构是符号表



## 1.1 Three-Address Code

```
x = y op z
```

之所以叫三地址吗，因为x,y,z通常代表了内存中的三个地址，但是x的地址的使用和y,z不同，因为y,z可以是常量或者没有运行时地址的字面常量

比如$2 * a + (b - 3)$

转换成三地址码：

```cpp
T1 = 2 * a;
T2 = b - 3;
T3 = T1 + T2;
```

其中T1, T2, T3是三个临时变量，这些临时变量对应于语法树的内部节点并且表示了他们的计算结果。他们有时候被保存在寄存器中，有时候保存在activation record中



Example:

```pascal
read x;
if 0 < x then
	fact := 1;
	repeat
		fact := fact * x;
		x := x - 1;
	until x = 0;
	write fact;
end
```

生成三地址码

```
read x
t1 = x > 0
if_false t1 goto L1
fact = 1

label L2
t2 = fact * x
fact = t2
t3 = x - 1
x = t3
t4 = x == 0
if_false t4 goto L2

write fact
label L1
halt
```

### 用于实现三地址码的数据结构

三地址码意味着每个表示需要4个域：**1个operation和3个地址**。

因此，一般用四元组(quadruple)的形式来实现

<img src="../../assets/images/image-20200601200019087.png" alt="image-20200601200019087" style="zoom:50%;" />

因为我们使用了变量，因此必须将其加入到符号表，以供进一步查询。

另一种替代方法是在四元式中使用指向符号表入口的指针， 避免额外的查询

三地址码使用自己的指令本身来代表临时变量，因此可以将四元组转变为三元组(triple)

<img src="../../assets/images/image-20200601200705268.png" alt="image-20200601200705268" style="zoom:40%;" />

## 1.2 P-code

P-machine包含

* a code memory
* an unspecified data memory for name variables
* a stack for temporary data
* whatever registers are needed to maintain the stack and support execution



Example1:

$2*a+(b-3)$

```
ldc 2 ;   load constant 2    (pushes 2 onto the temporary)

lod a ;  load value of variable a (pushes a onto the temporary)

mpi;    integer multiplication  (pops these two values from the stack, multiplies them (in reverse order), and pushes the result onto the stack.)

lod b ;   load value of variable b

ldc 3 ;   load constant 3

sbi   ;  integer subtraction(subtracts the first from the second)

adi   ;   integer additiom
```

Example2:

$x:=y+1$

```
lda  x    ;load address of x
lod  y    ;load value of y
ldc  1    ;load constant 1
adi       ;add
sto       ;store top to address below top  &  pop both
```

这段代码首先计算x的地址，然后将表达式的值赋给 x，最后执行sto命令，这个命令需 要临时栈顶上的两个值：一个是要被存储的值，在它下面的那个是值所要存入的地址。 sto指 令也弹出两个值(在这例子中使栈变空)。

#### P-代码和三地址码的比较 

P-代码在许多方面比三地址码更接近于实际的机器码。 P-代码指令也需要较少地址；我们已见过的都是一地址或零地址指令，另一方面， P-代码在指令数量方面不如三地址码紧凑， P -代码不是自包含的，指令操作隐含地依赖于栈 (隐含的栈定位实际上就是“缺省的”地址 )， 栈的好处是在代码的每一处都包含了所需的所有临时值， 编译器 不用如三地址码中那样为它们再分配名字。



# 2. Basic Code Generation Techniques

## 2.1 Intermediate Code as a Synthesized Attribute

如果把中间代码看作是节点的一个字符串属性，那么中间代码生成就可以被看成是一个属性计算。这个中间代码属于合成属性(孩子指向父亲), 并且能在分析期间直接通过语法树的后序遍历生成。

Example:

$exp\rightarrow id=exp\vert aexp$

$aexp\rightarrow aexp+factor\vert factor$

$factor\rightarrow (exp)\vert num\vert id$

token num和id有一个预先计算过的strval属性

$(x=x+3)+4$

#### P-code

```
lda x
lod x
ldc3
adi
stn
ldc 4
adi
```

<img src="../../assets/images/image-20200605001510046.png" alt="image-20200605001510046" style="zoom:50%;" />

其中: $++$插入新行，$\vert\vert$ 插入空格， stn的结果仍然留在stack中而sto的结果会被踢出

#### Three-address-code

我们称代码属性为tacode

三地码要求为表达式的中间结果生成临时变量名，这就要求属性文法 在每个节点中都包括一个新名字属性。这个属性也是合成的，如果没有为一个内部节点分配一 个新产生的临时名， 就用newtemp()产生一个临时名字系列 $t_1、t_2、t_3,\cdots$( 每次调用 newtemp( )就返回一个新的)

<img src="../../assets/images/image-20200605001837583.png" alt="image-20200605001837583" style="zoom:50%;" />

## 2.2 实际的代码生成

标准的代码生成或者涉及语法树后序遍历的修改

```pascal
procedure genCode(T:treenode);
begin
	if T is not nil then
		generate code to prepare for code of left child of T;
		genCode(left child of T);
		generate code to prepare for code of right child of T;
		genCode(right child of T);
		generate code to implement the action of T;
end;
```

注意，这个递归遍历不仅有一个后序部分(第7行: generate code to implement the action of T;)

还有一个前序部分(第4行: 为T的左子树产生准备代码)

还有一个中序部分(第6行: 为T的右子树产生准备代码)

<br>

假设我们的ast的定义如下

```cpp
typedefenum{Plus, Assign} optype;
typedefenum{OpKind, ConstKind, IdKind} NodeKind;
typedef struct streenode{ 
    NodeKindkind;
    Optypeop;  /* used with OpKind*/
    struct streenode* lchild, *rchild;
    int val; /* used with ConstKind*/
    char* strval;/* used for identifiers and numbers */
} STreenode;
typedef STreenode* syntaxtree;
```

表达式$(x=x+3)+4$的ast如下图所示

<img src="../../assets/images/image-20200607160043812.png" alt="image-20200607160043812" style="zoom:50%;" />

```cpp
%{
#define YYSTYPE char*
/* make Yacc use strings as values */
/* other inclusion code ... */
%}
%token NUM ID%
%exp       : ID { 
                 sprintf (codestr, "%s %s", "lda", $1);
                 emitCode ( codestr); 
            } 
			'=' exp { 
                emitCode ("stn"); 
            }
			| aexp
            ;
/*     */
aexp  : aexp  '+'  factor  { emitCode("adi");  } 
		| factor
        ;
factor: '(' exp  ')'
    	| num  {  
    		sprintf(codestr, "%s   %s",   "ldc", $1);
                    lemitCode(codestr);   
        }
        | ID   {  
            sprintf(codestr, "%s   %s",   "lod", $1);
            lemitCode(codestr);   
        }
        ;%%
```

## 2.3 Generation of Target Code from Intermediate Code

如果编译器或者直接从分析中或者从一棵语法树中产生了中间代码，那么下一步就是产生目标代码

最后的代码生成必须支持

* 变量和临时变量的实际定位
* 支持运行时环境所必需的代码
* 寄存器的合适定位和寄存器使用信息的维护(如哪个寄存器可用和哪个包含了已知值)



通常code generation from intermediate code有两种非常重要的技术

* macro expansion

  * 用一系列等效的目标代码指令代替每一条中间代码指令。(适用高级语言->低级语言)

* static simulation

  * 涉及到对中间代码效果的straight-line simulation, 并且生成匹配这些效果的目标代码。(适用低级语言->高级语言)

  

**接下来看一下用static simulation将P-code翻译到three-address-code的过程**

<img src="../../assets/images/image-20200607163209124.png" alt="image-20200607163209124" style="zoom:30%;" />

* 前三条指令执行后p-machine的状态如下，此时还没有三地址码产生

<img src="../../assets/images/image-20200607163329450.png" alt="image-20200607163329450" style="zoom:50%;" />

* 当处理`adi`时， 产生三地址码

```
t1 = x + 3
```

栈变成

<img src="../../assets/images/image-20200607163455555.png" alt="image-20200607163455555" style="zoom:50%;" />

* 处理`stn`, 产生三地址码

```
x = t1
```

栈变成

<img src="../../assets/images/image-20200607163536059.png" alt="image-20200607163536059" style="zoom:50%;" />

* `ldc 4`, 常量4入栈

<img src="../../assets/images/image-20200607163610047.png" alt="image-20200607163610047" style="zoom:50%;" />

* 处理`adi`， 产生三地址码

```
t2 = t1 + 4
```

<img src="../../assets/images/image-20200607163656590.png" alt="image-20200607163656590" style="zoom:50%;" />

* 完成静态模拟和翻译



**接下来用macro expansion从three-address-code翻译到p-code**

如：一个三地址指令

```
a = b + c
```

可以被翻译成如下的p-code指令序列

```
lda a
lod b
lod c
adi
sto
```



# 3. Code Generation of Data Structure References

## 3.1 Address Calculations

#### A. Three-Address Code for Address Calculations

使用与C语言中意义一样的"&"和"*"来指示地址模式

```
t1 = &x + 10
*t1 = 2
```

上述代码意思是把常量 2存放 在变量x加上10个字节的地址处



#### B. P-code for Address Calculations

* `ind` indirect load指令

用一个整型偏移量作为参数，假设栈顶上有一个地址，就将这个地址 与偏移量相加得到新地址，再将***新地址中的值***压入栈以代替原来栈顶的地址。

<img src="../../assets/images/image-20200607173626307.png" alt="image-20200607173626307" style="zoom:50%;" />

* `ixa` indexed address指令

用整型比例因子作为参数，假设一个偏移量已在栈顶并且在其下边有 一个基地址，则用比例因子与偏移量相乘，再加上基地址以得到新地址，再将偏移量和基地址从栈中弹出，压入***新地址***。

<img src="../../assets/images/image-20200607173654032.png" alt="image-20200607173654032" style="zoom:50%;" />

同样是这个例子

```
t1 = &x + 10
*t1 = 2
```

p-code可以写成

```
lda x
ldc 10
ixa 1
idc 2
sto
```



## 3.2 Array References

一般数组某元素地址的计算方式都是基址+偏移量

考虑C语言数组元素`a[i+1]`的地址是

```
a + (i + 1) * sizeof(int)
```

还要考虑，有些语言，数组的下标范围不是从0开始的。因此，任何语言中数组元素`a[t]`的地址为

```
base_address(a) + (t - lower_bound(a)) * element_size(a)
```

三地址码可以用`&a`获得`base_address(a)`

p-code可以用`lda a`将base_address(a)装入p-machine的stack中

`element_size(a)`在编译时就已经知道了，他会被替换为一个常量。



#### A. 数组引用的三地址码

三地址码引入了两个新的操作

* 一个是获取数组元素的值

```
t2 = a[t1]
```

* 还有一个是给数组元素赋值

```
a[t2] = t1
```

因此，源代码语句

```
a[i + 1] = a[j * 2] + 3
```

可以被翻译成如下三地址码

```
t1 = j * 2
t2 = a[t1]
t3 = t2 + 3
t4 = i + 1
a[t4] = t3
```

三地址码也有直接写出数组元素地址的计算

如:

```
t2 = a[t1]
```

可以被写成

```
t3 = t1 * elem_size(a)
t4 = &a + t3
t2 = *t4
```



#### B. 数组引用的p-code

使用指令`ixa`计算地址，`ind`用来装入已经计算地址里面的值

数组引用

```
t2 = a[t1]
```

翻译成p-code

```
lda t2
lda a
lod t1
ixa elem_size(a)
ind 0
sto
```

数组赋值

```
a[t2] = t1
```

翻译成p-code

```
lda a
lod t2
ixa elem_size(a)
lod t1
sto
```



#### C. 数组引用的代码生成过程

为了使用数组引用，更新文法如下

exp → subs = exp | aexp 

aexp → aexp + factor | factor

factor → ( exp ) | num | subs 

subs → id | id [ exp ]



... tbc



# 4. Code Generation of Control Statements and Logical Expressions

## 4.1 if和while语句的代码生成

if-stmt → if ( exp ) stmt | if ( exp ) stmt else stmt

while-stmt → while ( exp ) stmt

对这样语句的代码生成的主要问题是：将结构化的控制特性翻译成涉及转移的非结构化等价物， 它能被直接实现。这涉及到跳转

跳转可以被分成两类：unconditional jumps和jumps when the condition is false。 当condition为true的时候总是继续执行下面的指令，不需要jump

* unconditional jumps
  * `goto`(三地址码)
  * `ujp`(p-code)

* False jump:
  * `if_f, t1, L1,_`(三地址码)
  * `fjp L1` (P-code)

<img src="../../assets/images/image-20200607182650816.png" alt="image-20200607182650816" style="zoom:50%;" />



#### A. Three-Address Code for Control Statement

* if语句

```
if (E) S1 else S2
```

翻译成三地址码

```
<code to evaluate E to t1>
if_false t1 goto L1
<code for S1>
goto L2
label L1
<code for S2>
label L2
```

* while语句

```
while(E) S
```

翻译成三地址码

```
label L1
<code to evaluate E to t1>
if_false t1 goto L2
<code for S>
goto L1
label L2
```

#### B. P-code for control statement

* if语句

```
if(E) S1 else S2
```

翻译成p-code

```
<code to evaluate E>
fjp L1
<code for S1>
ujp L2
lab L1
<code for S2>
lab L2
```



* while语句

```
while(E) S
```

翻译成p-code

```
lab L1
<code to evaluate E>
fjp L2
<code for S>
ujp L1
lab L2
```

## 4.2 Generation of Labels and Backpatching

控制语句的代码生成有一个特点是:

jumps to a label must be generated prior to the definition of the label itself

因此，如果遇到forward jump(往还没有生成的代码跳转), 我们并不知道label到底在什么位置。

有一些方法来解决这个问题

* 在jump发生的代码处留下空位，或者产生一个dummy jump instruction to a fake location。当知道实际的转移位置后，这个位置用来回填(backpatch)缺少的代码

但是，这样依然会产生问题：

因为一般有两种jump, 一种short jump(含有128字节)，一种long jump(需要更多的空间)。

那code generator一般生成long jump占位，如果最后是short jump, 那么就插入nop指令来弥补这块多余的空间。



## 4.3 Code Generation of Logical Expressions

这里讨论`and`,` or` 操作，这两个操作存在短路现象

因此，可以将它们写成if语句

`a and b` $\equiv$ `if a then b else false`

`a or b` $\equiv$ `if a then true else b`

看一个例子:

```
(x != 0) && (y == x)
```

翻译成p-code

```
lod x
ldc 0
neq
fjp L1 {如果为false短路，跳转到L1}
lod y
lod x
equ
ujp L2
lab L1
lod FALSE {第一个条件就为false}
lab L2 
```



## 4.4 if和while语句的代码生成过程样例

stmt → if-stmt | while-stmt | break | other 

if-stmt → if( exp ) stmt | if( exp ) stmt else stmt 

while-stmt → while( exp ) stmt 

exp → true | false



tbc



# 5. 过程和函数调用的代码生成

## 5.1 Intermediate Code for Procedures and Functions

函数调用的中间代码表示的要求可明确地表述如下：

* 首先， 有两个实际的机制需要说明 —
  * 函数过程的定义definition (也叫声明declaration) 
    * 定义产生了函数名、参数和代码，但函数却不在那点执行。
  * 函数过程的调用(call) 。
    * 调用产生了参数的实际值，并且执行一个到函数代码的转移。函数代码被执行和返回。当产生函数代码时，除了它的一般结构，执行的运行时环境还不知道其他情况。这个运行时环境部分由调用者建造，部分也由被调用函数代码建造。 任务的分隔是调用次序(calling sequence) 的一部分，



函数定义的intermediate code必须

* 有一条标志函数定义开始的指令(也称为函数代码的entry point)
* 有一条标志函数定义结束的指令(也称为函数返回点return point)

```
Entry instruction
<code for function body}
Return instruction
```



函数调用的intermediate code必须

* 有一条指令指示参数计算的开始 (为调用而准备的)
* 然后实际调用指令指示已经参数已经构成，到函数代码的actual jump可以发生

```
Begin-argument-computation instruction 
<code to compute the arguments> 
Call instruction
```



### A. Three-Address Code for Procedures and Functions   

#### i. 定义

* 在三地址码中， 入口指令需要给出一个procedure entry point的名字， 这类似于label指令，因此，这是一条one-address instruction。我们将其简单地称作entry
* 类似地把返回指令叫作return。这个指令也是一条one-address instructio，假如有返回值，那么它必须给出返回值的名字

```cpp
int f(int x, int y) {
    return x + y + 1;
}
```

翻译成如下三地址码

```
entry f
t1 = x + y
t2 = t1 + 1
return t2
```



#### ii. 调用

在调用的情况下，实际需要 3个不同的三地址指令：

* 一个标志参数计算的起点，称作begin_args(是一个zero-address instruction)；
* 一个被用于重复地说明参数值的名字的指令，我们称其为arg(它必须包括参数值, 地址或名字 )；
* 最后是实际调用指令，简单地写成 call，它也是一个一地址指令(被调用函数的名字或入口点必须给出)。

例如前面所述函数定义`f`的调用

```
f(2 + 3, 4)
```

翻译成三地址码

```
begin_args
t1 = 2 + 3
arg t1
arg 4
call f
```

这里我们从左到右列出参数，但实际上可以有不同的顺序



### B. P-Code for Procedures and Functions 

#### i. 定义

P-代码的入口指令是`ent`，返回指令是`ret`，前面C函数`f`的定义可以翻译成P-Code如下

```
ent f
lod x
lod y
adi
ldc 1
adi
ret
```

`ret`指令不需要一个参数用来指示返回的值, 返回值默认在栈顶

#### ii. 调用

p-code中使用`mst`和`cup`指令

* `mst` (mark stack): 对应于`begin_args`
* `cup` (call user procedure) 对应于`call`

在遇到`cup`指令时，将所有参数值都假想成出现在栈顶 (按适当的顺序)



## 5.2 函数定义和调用的代码生成过程





# 代码优化技术

## 1 代码优化的主要来源

下面将列出某些代码生成器不能产生好代码的地方， 粗略地减少"代价"，也就是在这些地方代码可以获得多大改进

#### A. 寄存器分配

寄存器很少，通常只有8个或者16个或32个寄存器，其中包括了特殊用途寄存器，比如`pc`, `sp`, `fp`。这使得寄存器合理分配很困难，因为变量和临时变量竞争空间很激烈。

* 解决这个问题的一个方法是：增加在内存执行的操作的数量和速度。

这样，就可以避免在编译器用完寄存器空间后，存储寄存器值到临时变量以释放寄存器值然后再装新值的代价。(register spill代价)

* 另一个方法(RISC方法)， 减少在内存直接执行的操作的数量(通常为0)， 但同时增加可用寄存器到32, 64, 128个。

这种结构中分配 寄存器失误的代价是频繁装载和存入值。同时因为有了很多可用的寄存器，寄存器分配工作也变得简单了。这样，提高代码质量的重要努力应着眼于合理分配寄存器。



#### B. 不必要操作

避免产生冗余或不必要的操作代码。这种优化从很简单的搜索局部代码到分析整个程序的语法特性各不相同。 

这种优化的一种典型例子是代码中重复出现的表达式，而且它们的值相同， 可以保存的第1次的计算值并删除重复计算 (称为公共子表达式消除 (common subexpression elimination)) 。

另一个例子是避免存储不再使用的变量或临时变量的值 (这要与前面的优化共同进行)。

全程的优化涉及识别不可到达 (unreachable)或死代码(dead code)，典型例子是使用常量标志开关调试信息：

```cpp
#define DEBUG 0
...
if(DEBUG)
{ ... }
```

如果DEBUG设为0(如代码中所示)，那么在if语句的大括号内的代码将不可到达，于是就不必产生这段目标代码，甚至连if语句也可以省掉。

另一个不可到达代码例子是不调用的过程 (或只在不可到达的代码处调用)的消除，不可到达代码不总是对速度产生重大影响，不过可以真正减少目标代码大小，这是值得的，特别是当分析中很小的代价就可以识别大部分明显的情况



#### C. 高代价操作costly operations

比如$x^3$，用连乘$x*x*x$实现。乘2可以用移位操作实现。

这种优化称为reduction in strength。

**常数处理**

* constant folding: 比如2+3直接用常数5代替
* constant propagation: 有些变量在程序的局部或者全程都没有修改，具有恒定值，这样就可以用常量来代替。



**过程处理**

* procedure in lining(过程内嵌)：replacing the procedure call with the code for the procedure body(with suitable replacement of parameters)
* Recognize tail recursion: when the last operation of a procedure is to call itself. 消除尾递归



#### D. 预测程序行为Prediction Program Behavior

为了实施前面描述的一些优化，编译器必须收集有关程序中变量、值和过程使用的信息：表达式是否重用 (可变成公共子表达式)、变量是否改变、何时改变或一直不变、过程是否调用。编译器必须在计算技术范围内作最坏的假设，收集的信息有可能产生错误 的代码：变量在特定点可能恒定也可能不恒定、编译器必须假设它不恒定，这意味着必须将编译器做成不是最优化的， 甚至常常对程序的行为信息利用较差。 

另一个许多编译器采用的方法是：实际运行中***采集程序行为的统计信息***，然后用于预测哪条分支最有可能运行、哪个过程经常调用以及哪部分代码最经常执行。这些信息可以用于调整转移结构，循环和过程代码来最小化最经常执行部分的运行时间



## 2. 优化分类

#### 首先我们考虑应用程序在编译过程中的时间。 

优化可以在编译的每阶段分别执行。 例如， 常数合并可以在分析时进行。 另一方面， 一些优化可以延迟到目标代码生成之后—检查并重写目标代码以反映优化。 

有时因为通常只观察目标代码的一小部分来进行优化， 所以在目标代码上进行的优化称作为***窥孔优化 (peephole optimization))***。

通常，主要的优化工作在中间代码生成部分、中间代码生成后或目标代码生成部分。

* 源代码级优化(source-level optimization))，
* 目标代码级优化(target-level optimization)

有时一种优化可以同时有源码级部分和目标码级部分。例如，在寄存器分配中，经常要计算变量引用次数并将高引用率的变量放入寄存器。这个任务又分成了一个源码级部分，这里为选择的变量分配寄存器不 必知道有多少可用寄存器。然后寄存器赋值步骤依赖于目标机器为这些标记的变量分配实际的 寄存器，或到称为伪寄存器(pseudoregister)的内存(万一没有可用寄存器的话)。

<br>

在不同的优化中，考虑某一优化对其他优化产生的影响相当重要。例如，应在执行不可到达代码消除之前进行传播常量操作，这是因为在测试变量被发现是常数据之后，某些代码会变得不可大到达。 在偶然情况下会发生两种优化无法为对方发现进一步优化机会的阶段问题

<img src="../../assets/images/image-20200609213246319.png" alt="image-20200609213246319" style="zoom:35%;" />

#### 考虑的是优化应用的程序范围

这类优化还分为

* 局部 (local)
* 全程(global)
* 过程间(interpocedural)优化。

**local optimizations:** 为应用于代码的线性部分 (straight-line segment of code)的优化， 也就是代码中没有jump语句 。 一个最大的线性代码序列称为基本块 (basic block)。

**global optimizations:** applied to an individual procedure.  Loop discovery are necessary
**Interprocedural optimizations:** extend beyond the boundaries of procedures to the entire program.



## 8.3 Data structures and Implementation Techniques for Optimizations

一些优化可以在语法树的变换上实现。这些包括常数合并和不可到达代码消除，通过删除 或以简单形式替换相应的子树来实现。 用于后面优化的信息也可以在构造和遍历语法树时收集， 比如引用次数和其他有用信息， 保存到树的属性或符号表项中。但是语法树这个结构不是普适的。以下给出一些普适的数据结构

#### Flow Graph

流图的节点为基本块， 边是来自conditional or unconditional jump。每个基本块节点包含块的中间代码序列。

通过对中间代码的一次遍历，可以建立flow graph

1) 第1条指令开始一个新基本块。

2) 每个转移目的标签开始一个新基本块。

3) 每条跟随在转移之后的指令开始一个新基本块。

<img src="../../assets/images/image-20200609214716444.png" alt="image-20200609214716444" style="zoom:50%;" />

流图是数据流分析的主要数据结构，积累信息用于优化。

计算所有变量的可达定义(reaching definition)集是一个标准的数据流分析问题



#### 为基本块构造DAG