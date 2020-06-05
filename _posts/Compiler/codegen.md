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

```cpp
typedefenum{Plus, Assign} optype;
typedefenum{OpKind, ConstKind, IdKind} NodeKind;
typedef struct streenode{ 
    NodeKindkind;Optypeop;  /* used with OpKind*/
    struct streenode* lchild, *rchild;
    int val; /* used with ConstKind*/
    char* strval;/* used for identifiers and numbers */
} STreenode;
typedef STreenode* syntaxtree;
```

