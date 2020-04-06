---
layout: article
title: "Compilers--Bottom-Up Parsing"
tag: Compiler
key: page-compilerbuparsing
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



<img src="../../../assets/images/test.png" alt="image-20200402232034726" style="zoom:50%;" />

自底向上语法分析器从叶节点开始构建语法分析树，从叶节点向根节点方向进行。语法分析器对词法分析器返回的每个token分别构建一个叶节点。这些叶节点形成了语法分析树的下边缘。为了构建一个推导，语法分析器需要根据语法和语法分析树部分完成的底部，在叶节点之上添加非终结符层。

为向上扩展语法分析树的上边缘，语法分析器考察当前的边缘，寻找一个与某个产生式 $A\rightarrow \beta$ 的右侧句型相匹配的字串。如果在语法分析树的上边缘找到$\beta$, 且右端位于k,那么语法分析器可以将$\beta$替换为A, 从而创建新的上边缘。

如果在输入串的有效推导中，下一步是在位置k用A替换$\beta$, 那么$(A\rightarrow\beta,k)$是当前推导中的一个句柄(handle),语法分析器应该用A替换$\beta$, 这种替换称为**归约(reduction)**, 因为他减少了上边缘符号的数目。如果语法分析器正在构建一个语法分析树，他会为A构建一个节点，并将该节点添加到树中，将表示$\beta$的各个节点连接到A作为子节点

# 1. Overview of Bottom-Up Parsing

自底向上的语法分析器会用到一个explicit stack. 这个parsing stack可以同时包含tokens以及nonterminals.

最初stack为空，然后将Input String中的token一个一个放入stack, 每次token进入stack, stack都会判断这步能不能将stack中的元素转换到一个'级别'更高的nonterminal, 最终输入串为空并且stack中只留下start symbol时，分析成功。

<br>

Example:

$S\rightarrow S+E\vert E$

$E\rightarrow num\vert (S)$

<img src="../../../assets/images/image-20200402233459512.png" alt="image-20200402233459512" style="zoom:40%;" />

input string为`(1+2+(3+4))+5`

先将`(`入栈，不能转换。再将`1`入栈，发现$E\rightarrow num$, 因此将`1`出栈，将`E`入栈，接下来的操作同理。

<br>

对于LL(k)中的generate, match这两个操作，这里也有两个非常重要的操作：***shift*** & ***reduce***

{:.success}

***Shift***: shift a terminal from the front of the input to the top of the stack

{:.success}

***Reduce***: Reduce a string $\alpha$ at the top of the stack to a nonterminal A, given the BNF choice $A\rightarrow\alpha$

<br>

还要注意一个点：

A further feature of bottom-up parsers: grammars are always augumented with a ***new start symbol***. If S is the start symbol, a new start symbol S' is added to the grammar: $S'\rightarrow S$

<br>

<img src="../../../assets/images/image-20200403170841057.png" alt="image-20200403170841057" style="zoom:50%;" />

接下来会介绍一些术语

* ***Right Sentential Form***:
  * Each of the intermediate strings of terminals and nonterminals in such a derivation is called a right sentential form.
  * Each such sentential form is **split** between the parsing stack and the input during a shift-reduce parse.

* ***viable prefix***
  * The sequence of symbols on the parsing stack is called a viable prefix of the right sentential form. 
  * `E`,` E+`, `E+n` are all viable prefixes of the right sentential form `E+n`
  * `n+` is not a viable prefix of `n+n`. (因为在`+`入栈时，`n`一定被reduce为`E`了, 所以`n+n`不可能在stack中存在)
* ***Handle***
  * The **string**, together with the **position** in the right sentential form where it occurs, and the **production** used to reduce it , is called the **handle** of the right sentential form.
  *  $<A\rightarrow\beta,k>$, 其中$\beta$出现在语法分析树的上边缘，其右侧末端位于位置k, 且将$\beta$替换为A是语法分析中的下一步。

{:.error}

Determining the next handle in a parse is the main task of a shift-reduce parser.

<br>

# 2. Finite automata of LR(0) items and LR(0) parsing

LR(k): 从左至右分析(from Left to right), 最右推导(rightmost derivation), 超前查看k个token(k-token lookahead)

<br>

## 2.1 LR(0) items

An LR(0) item of a context-free grammar: a production choice with a distinguished position in its right-hand side.(一个产生式再加上一个位于它的体中某处的点)

比如, $A\rightarrow\alpha,\beta\gamma=\alpha$, 那么$A\rightarrow\beta\cdot\gamma$就是一个LR(0) item.

这里$\cdot$代表的是位置信息，$\beta$已经在stack里面了，而$\gamma$还在input string中

<br>

Example:

$E\rightarrow E'$, $E\rightarrow E+n\vert n$

LR(0) items:

$E'\rightarrow \cdot E$

$E'\rightarrow E\cdot$

$E\rightarrow\cdot E+n$

$E\rightarrow E\cdot +n$

$E\rightarrow E+\cdot n$

$E\rightarrow E+n\cdot$

$E\rightarrow \cdot n$

$E\rightarrow n\cdot$

<br>



## 2.2 Finite Automata of Items

LR(0) items代表了有穷自动机的状态

This will start out as a nondeterministic finite automaton.

Construct the DFA of sets of LR(0) items using the subset construction from this NFA of LR(0) items.

Construct the DFA of sets of LR(0) items directly.



<img src="../../../assets/images/image-20200403193404929.png" alt="image-20200403193404929" style="zoom:50%;" />

如上图所示， 左边$A\rightarrow\alpha\cdot X\eta$和右边$A\rightarrow\alpha X\cdot\eta$代表了两个状态。左边的状态接收到了X之后转换到右边的状态

下面对于X的类别来做一个讨论

* 如果X是一个token, 那么这个状态转移就相当于把在input string中X移动到了分析栈的栈顶
* 如果X是一个nonterminal
  * X不会作为input symbol出现，只可能通过一个$X\rightarrow\beta$的归约出现。

<img src="../../../assets/images/image-20200403194518092.png" alt="image-20200403194518092" style="zoom:50%;" />

NFA的起始状态等价于parser的起始状态，此时stack为空

S: start symbol of the grammar

any initial item $S\rightarrow\cdot\alpha$:  a start state

当然，前面提到增广文法，也就是加上 $S'\rightarrow S$

所以，initial item $S'\rightarrow\cdot S$ is the start state of the NFA

<img src="../../../assets/images/image-20200403201722976.png" alt="image-20200403201722976" style="zoom:50%;" />

在LR文法中，将NFA转换为DFA会用到闭包函数

LR(0) item的闭包:

如果I是文法G的一个item, 那么CLOSURE(I)就是根据下面的两个规则从I构造得到的item集合

* 一开始，将I中的各项加入CLOSURE(I)
* 如果$A\rightarrow\alpha\cdot B\beta$在CLOSURE(I)中，也就是说当前position后面跟着一个非终结符，$B\rightarrow\gamma$是一个产生式，并且项$B\rightarrow\cdot\gamma$不在CLOSURE(I)中，就把这项加入其中。不断应用这个规则，直到没有新项可以加入到CLOSURE(I)中为止。

那为什么要这样做呢？

CLOSURE(I)中的项$A\rightarrow\alpha\cdot B\beta$表明在语法分析过程的某点上,$\alpha$已经在栈中，我们期待后面是$B\beta$，这样就可以归约到A。因为B是一个非终结符，我们认为可能会在输入中看到一个能够从B推导得到的子串，而推导时需要应用某个B产生式。因此将各个B产生式对应的项加入闭包中

<br>

通过闭包的计算，将NFA转换为DFA

<img src="../../../assets/images/image-20200403202728910.png" alt="image-20200403202728910" style="zoom:50%;" />

## 2.3 The LR(0) Parsing Algorithm

相较于LL(k)文法, LR(0)文法的stack中不仅存放**symbols**(也就是终结符+非终结符)，还存放**state numbers**（状态机对应的状态编号）

当把一个symbol push到parsing stack后，也要把new state number push到parsing stack



<img src="../../../assets/images/image-20200403203442211.png" alt="image-20200403203442211" style="zoom:50%;" />

上图是两种表达方式，但殊途同归。第二种是把symbol和state number分开了。

`Definition`{:.success}

令s表示当前状态(at the top of the parsing stack). The actions are defined as follows:

1. 如果状态s包含了$A\rightarrow\alpha\cdot X\beta$(X是终结符)，那么就执行**shift**操作，将当前的input token 移动到stack中
2. 如果状态s包含了complete item($A\rightarrow\gamma\cdot$的形式)，那么就进行根据规则$A\rightarrow\gamma\cdot$进行**reduce**
   * A reduction by the rule $S'\rightarrow S$, where S' is the start state
   * `Acceptance`{:.error} if the input is empty
   * `Error`{:.success} if the input is not empty

如果以上规则unambiguous,则这个文法被称为LR(0)文法

一个文法是LR(0)文法当且仅当

* Each state is a shift state(只含有shift items的状态)
* A reduce state containing a single complete item

因为LR(0)并不超前查看，因此每个状态只能对应一个动作，否则就会产生歧义。这个动作要么是shift, 要么是reduce,也就是不能有shift-reduce conflict。同时，一个状态也不能含有两个以上的归约项目，否则就会需要look ahead看要选择哪方式，也就是不存在reduce-reduce conflict.

<br>

Example: Consider the grammar $A\rightarrow (A)\vert a$

<img src="../../../assets/images/image-20200403224414932.png" alt="image-20200403224414932" style="zoom:50%;" />

<img src="../../../assets/images/image-20200403224631644.png" alt="image-20200403224631644" style="zoom:50%;" />

<br>

为了找起来更方便，将状态转移图转换成Parsing table. 这样，LR(0) parsing就变成了一种table-driven parsing method

介绍一下goto函数

goto(I,X), 其中I是一个项集$A\rightarrow\alpha X\cdot\beta$的闭包，X是一个文法符号，I接收非终结符X从I状态转移到另一个状态。

表格中，GOTO接收非终结符

<img src="../../../assets/images/image-20200403224933750.png" alt="image-20200403224933750" style="zoom:50%;" />

$s_i$表示shift并将状态i压栈

将每条规则标上号

$r_j$表示按照编号为j的产生式规则进行reduce

<img src="../../../assets/images/image-20200403225900491.png" alt="image-20200403225900491" style="zoom:50%;" />

# 3. The SLR(1) Parsing Algorithm

SLR(1): S--> simple

`Definition`{:.success}

SLR(1) parsing algorithm:

输入: 一个增光文法G'

方法:

1. 构造G'的规范LR(0)项集族$C=\{I_0,I_1,\cdots,I_n\}$

2. 根据$I_i$构造得到状态i. 状态i的语法分析动作按照下面的方法决定

   * 如果$[A\rightarrow\alpha\cdot a\beta]$在$I_i$中并且$GOTO(I_i,a)=I_j$,即$I_i$字串接收a之后转移到$I_j$子串，那么将$ACTION[i,a]$设置为shift j. 注意，这里的a是一个终结符
   * 如果$[A\rightarrow\alpha\cdot]$在$I_i$中，那么对于$FOLLOW(A)$中的所有a, 将$ACTION[i,a]$设置为reduce $A\rightarrow\alpha$。注意，这里A不等于S'
   * 如果$[S'\rightarrow S\cdot]$在$I_i$中，那么将$ACTION[i,\$]$设置为accept

   如果根据上面的规则生成了任何冲突动作，那么这个文法就不是SLR(1)的

3. 状态i对于非终结符A的GOTO转换使用下面的规则构造得到: 如果$GOTO(I_i,A)=I_j$, 那么$GOTO[i,A]=j$

4. 如果规则2和3没有定义的所有条目都设置为报错

5. 语法分析器的初始状态就是根据$[S'\rightarrow\cdot S]$所在项集构造得到的状态

<br>

一个文法是SLR(1)文法如果他的SLR(1) parsing rules是无二义性的

一个文法是SLR(1)文法当且仅当，对任何状态s, 以下两个条件满足:

1. For any item $A\rightarrow\alpha\cdot X\beta$ in state `s` with $X$ a terminal, there is no complete item $B\rightarrow\gamma\cdot$ in `s` with $X$ in Follow(B)
2. For any two complete items $A\rightarrow\alpha\cdot$ and $B\rightarrow\beta\cdot$ in s, $Follow(A)\cap Follow(B)$ is empty. 

看一下LR(0)和SLR(1)的条件

LR(0)要求不能存在shift-reduce冲突，也就是shift和reduce对应的字串不能共存于一个状态中，但是SLR(1)放松了这个限制，见规则1。也就是说$A\rightarrow \alpha\cdot X\beta$这个shift对应的字串和$B\rightarrow\gamma\cdot$这个reduce对应的字串可以共存于一个状态，但是X不能在FOLLOW(B)中，否则就会产生shift X和reduce B这两个动作的矛盾。

LR(0)要求不能存在reduce-reduce冲突。也就是说两个complete item不能共存于一个状态。SLR(1)也放松了这个限制，见规则2。$A\rightarrow\alpha\cdot$和$B\rightarrow\beta\cdot$共存于状态s。 如果后面的文法符号在FOLLOW(A)中，就根据$A\rightarrow\alpha\cdot$进行reduce;如果后面的文法符号在FOLLOW(B)中,就根据$B\rightarrow\beta\cdot$进行reduce。因此为了不产生冲突,FOLLOW(A)和FOLLOW(B)就不能有交集。

<br>



<img src="../../../assets/images/image-20200405161627917.png" alt="image-20200405161627917" style="zoom:50%;" />

### Disambiguating rules for parsing conflicts

SLR(1) parsing有两种parsing conflicts

* shift-reduce conflicts
* reduce-reduce conflcits

对于shift-redece conflcits, 有天然的disambiguating rule: always prefer the **shift** over the **reduce**

对于reduce-reduce conflicts就有点麻烦了，一般会认为这种conflict为error

<br>

# 4. General LR(1) and LALR(1) parsing

SLR(1)的问题在于: 他是在LR(0) item构建的DFA的基础之上应用了lookahead.

general LR(1)的power在于他充分利用了lookahead

他使用了LR(1) item去构建新的DFA, lookahead从开始就已经包含了DFA之中了。

<br>

### 4.1 Finite Automata of LR(1) Items

`Definition`{:.error}

Write LR(1) items using square brackets as

$$[A\rightarrow\alpha\cdot\beta,a]$$

这里$A\rightarrow\alpha\cdot\beta$是一个LR(0) item并且a是一个token(lookahead), a代表的是A后面可以跟着的符号

<br>

LR(0)和LR(1)自动机的最重要的区别是: Definition of the $\varepsilon$-transitions

`Definition`{:.error}

LR(1) transitions:

* 给定一个LR(1) item $[A\rightarrow\alpha\cdot X\gamma,a]$, $X$可以是终结符也可以是非终结符,通过$X$可以转移到$[A\rightarrow\alpha X\cdot\gamma,a]$

* 给定一个LR(1) item $[A\rightarrow\alpha\cdot B\gamma,a]$, 这里B是一个非终结符，存在$\varepsilon$-transitions转移到$[B\rightarrow\cdot\beta,b]$, 这里b属于集合$First(\gamma a)$

<br>

对于LR(1)文法的自动机

The start state: Augmenting the grammar with a new start symbol S', $S'\rightarrow S$

The start symbol of the NFA of LR(1) items becomes the item

$$[S'\rightarrow\cdot S,\$]$$

<br>

### 4.2 The LR(1) parsing algorithm

The general LR(1) parsing algorithm:

令s为当前状态(a在parsing stack的栈顶). 定义如下操作

* 如果LR(1) item的形式为$[A\rightarrow\alpha\cdot X\beta,a]$, X是一个终结符，并且X是输入串的next token
* complete LR(1) item $[A\rightarrow\alpha\cdot,a]$, 输入串的next token是a
* 

Let s be the current state (a the top of the parsing stack). Then actions are defined as follows:

输入:一个增广文法G'

方法:

1. 构造G'的LR(1)项集族 $C'=\{I_0,I_1,\cdots,I_n\}$

2. 语法分析器的状态i根据$I_i$构造得到。状态i的语法分析动作按照下面的规则确定

   * 如果$[A\rightarrow\alpha\cdot a\beta,b]$在$I_i$中，并且$GOTO(I_i,a)=I_j$, 那么将$ACTION[i,a]$设置为shift 到状态j。 注意，这里`a`是一个终结符
   * 如果$[A\rightarrow\alpha\cdot,a]$在$I_i$中并且$A\not=S'$, 那么$ACTION[i,a]$设置为reduce $A\rightarrow\alpha$
   * 如果$[S'\rightarrow S\cdot,\$]$在$I_i$中，那么将$ACTION[i,\$]$设置为accept

   如果根据上述规则会产生任何冲突动作，我们据说这个文法不是LR(1)的

3. 状态i相对于各个非终结符A的goto转换按照下面的规则构造得到:如果$GOTO[I_i,A]=I_j$, 那么$GOTO[i,A]=j$

4. 所有没有按照规则2,3定义的分析表条目都设置为报错

5. 语法分析器的初始状态是由包含$[S'\rightarrow\cdot S,\$]$的项集构造得到的状态。

<br>

一个文法是LR(1)文法，当且仅当对任何状态`s`, 以下两个条件满足

1. 状态`s`中的一项$[A\rightarrow\alpha\cdot X\beta,a]$，其中$X$是一个非终结符，状态`s`中不能存在形如$[B\rightarrow\gamma\cdot, X]$的项(否则会出现shift-reduce conflict)
2. 状态`s`中没有两个项，$[A\rightarrow\alpha\cdot, a]$和$[B\rightarrow\beta\cdot,a]$(否则会出现reduce-reduce conflict)

<img src="../../../assets/images/image-20200405183640362.png" alt="image-20200405183640362" style="zoom:50%;" />



### 4.3 LALR(1) parsing

考虑到LR(1) item构建的DFA的size太大，我们是不是可以进行一些状态合并呢？

比如下图中$A\rightarrow a \cdot,\$ $和$A\rightarrow a\cdot,)$就比较相似， 他们的first component是一样的，但是second component(也就是lookahead symbols)不相同，是不是可以将它们合并为$A\rightarrow a\cdot,\$ /)$

<img src="../../../assets/images/image-20200405184111768.png" alt="image-20200405184111768" style="zoom:50%;" />

LALR(1) parsing algorithm就是识别上述状态并且将它们的lookaheads进行合并， 这样就可以达到LR(0)状态数的量级

<br>

接下来介绍principles of LALR(1) parsing

1. The core of state of DFA of LR(1) items is the state of the DFA of LR(0) items, 也就是LR(1) item中的第一部分
2. 如果LR(1)项的DFA中的两个状态$s_1,s_2$相同的core, 假设从状态$s_1$接收$X$后转移到到状态$t_1$。那么同样有状态$s_2$接收$X$后转移到状态$t_2$, 并且$t_1$和$t_2$有相同的core。

<img src="../../../assets/images/image-20200405185222796.png" alt="image-20200405185222796" style="zoom:50%;" />



如果一个文法是LR(1)文法，那么LALR(1) parsing table不可能有shift-reduce conflicts, 但是可能有reduce-reduce conflicts.

<img src="../../../assets/images/image-20200405185420660.png" alt="image-20200405185420660" style="zoom:50%;" />

如果一个文法是SLR(1)文法，那他就一定是LALR(1)文法

LALR(1) parsers often do as well as general LR(1) parsers in removing typical conflicts that occur in SLR(1) parsing.

If the grammar is already LALR(1), the only consequence of usign LALR(1) parsing over general LR parsing as following: in the presence of errors, some spurious reductions may be made before error is declared.

Compute the DFA of LALR(1) items directly from the DFA of LR(0) items through a process of propagating lookaheads.











