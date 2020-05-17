---
layout: article
title: "Compilers--Top Down Parsing"
tag: Compiler
key: page-compilertdparsing
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
  background_image:
    src: https://miaochenlu.github.io/picture/image-20191208000900444.png.png



---

Top-Down Parsing

<!--more-->

<style>
  .page__header .header__brand path {
    fill: rgba(255, 255, 255, .95);
  }
</style>

<br/>

<img src="../../../assets/images/mstile-150x150.png" alt="image-20200316095402390" style="zoom:50%;" />

# 1. Top-Down Parsing by Recursive-Descent

## 1.1 The idea of Recursive-Descent Parsing

$A\rightarrow\alpha$

* The grammar rule for a non-terminal A as a <u>definition for a procedure</u> to recognize an A.  可以看作非终结符的一个定义，A是定义对象，$\alpha$是定义内容

* The right-hand side of the grammar for A specifies the structure of the code for this procedure. (A是函数名，$\alpha$是函数体)

手写文法分析器的常用手段，实现方式简单。

<br>

> Example: the expression Grammar
>
> expr→ expr addop term$\vert$term
>
> addop → + -
>
> term→ term mulop factor $\vert$ factor
>
> mulop → *
>
> factor → (expr) $\vert$ number

```pascal
Procedure factor
BEGIN
  case token of
  (: 						{case '('}
	  match((); 	{ match '(' , true->getNextToken}
    expr;				{函数调用，无参数}
    match());		{match ')' }
  number:				{case number}
    match(number);
  else error;
  end case;
END factor
```

Writing recursive-decent procedure for the remaining rules in the expression grammar is not as easy for factor.

It requires the use of EBNF. {} 循环 []一次或者0次

## B. Repetition and Choice: Using EBNF

在同时使用经典表达式语法和最左匹配的自顶向下语法分析器时，语法本身的结构导致出现了一个问题。

`a+b*c`

<img src="../../../assets/images/image-20200326201319268.png" alt="image-20200326201319268" style="zoom:50%;" />

从Expr开始，试图匹配a, 他应用规则1，在语法分析树的下边缘创建句型$Expr+Term$。现在，语法分析器再次面临非终结符Expr和输入单词a。 如果选择是一致的，他应该应用规则1，用$Expr+Term$替换$Expr$。不断重复

之所以会出现这个问题，是因为语法在产生式中使用了**左递归**。在使用左递归的情况下，自顶向下语法分析器可能会无限循环，而不会生成与输入匹配的起始终结符(也不会前移输入位置)。

> Example
>
> `If-stmt → if ( exp ) statement |  if ( exp ) statement else statement`
>
> The EBNF of the if-statement is as follows:
>
> `If-stmt → if ( exp ) statement [ else statement]`



> Example: expr → expr addop term｜term
>
> &emsp;this would lead to an immediate ***infinite recursive loop***.
>
> &emsp;both exp and term can begin with the same tokens (a number or left parenthesis).

The solution is to use the EBNF rule:

&emsp;expr → term {addop term}

```pascal
Procedure expr;
Begin
	term;
	while token = + or token = - do
		match(token);
		term;
	End while;
End expr;
```

The solution is to use the EBNF rule:

&emsp;term → factor {mulop factor}

```pascal
procedure term;
Begin
	factor;
	while token = * do 
		match(token);
		factor;
	End while;
End term;
```

<br>

**Q**: 由花括号(和原始的BNF中的显式)表示的左**left associative**是否仍然保留

如下所示temp := temp+term

```pascal
function expr : integer;
  var temp: integer;
  begin
    temp := term;
    while token = + or token = - do
      case token of
        +: match(+);
          temp := temp + term;//如何确保左结合
        -: match(-);
          temp := temp - term;
      end case;
    end while;
  return temp;
end exp;
```

{:.warning}

总结：把BNF改造成EBNF(不能出现左递归),然后写递归下降程序

<br>

### C. Further Decision Problems

The recursive-descent method is quite powerful and adequate to construct a complete parse. But we need more formal methods to deal with complex situation.

&emsp;1. It may be difficult to convert a grammar in `BNF`{:.warning} into `EBNF`{:.warning} form

&emsp;2. It is difficult to decide when to use the choice $A\rightarrow\alpha$ and the choice $A\rightarrow\beta$; if both $\alpha$ and $\beta$ begin with ***non-terminals***. (<u>First Sets</u>)

&emsp;3. It may be necessary to know what token legally coming from the non-terminal A, $A\rightarrow\varepsilon$. (<u>Follow Sets</u>) 非终结符后面可以跟什么token

&emsp;4. It requires computing the <u>First and Follow</u> sets in order to detect the errors as early as possible.

<br>

最左匹配的自顶向下语法分析器中，低效的主要原因是回溯。如果语法分析器用错误的产生式扩展语法分析树的下边缘，在语法分析树的下边缘与词法分析器返回的单词之间，最终会出现不匹配的情况。在语法分析器发现这种不匹配情形时，它必须撤销构建出错误的语法分析树下边缘的操作，并尝试产生其他产生式。扩展，收缩，再扩展语法分析树下边缘的操作费时费力。

如果语法分析器在选择下一条规则时，可以同时考虑当前关注的符号以及下一个输入符号(lookahead symbol)，可以消除在解析右递归表达式语法时多种选择造成的不确定性。因而，我们说该语法在look ahead 1个符号时是无回溯的。无回溯语法也称预测性语法。

# 2. LL(1) Parsing



first L--> from left to right(比如有一个expr $3+4+5$, 我们就会从3开始处理，最后是5)

second L--> leftmost derivation

(1)-->look ahead 1 token(往前看的越多，能力越强，但是效率会低)

<br>

LL(1) Parsing uses an **explicit stack** rather than recursive calls to perform a parse;[recursive descend parsing 用的是recursive, 可以用stack来代替]

> Example:
>
> $S\rightarrow E+S\vert E$
>
> $E\rightarrow num\vert (S)$

<img src="../../../assets/images/image-20200321204355184.png" alt="image-20200321204355184" style="zoom:30%;" />

Want to decide which production to apply based on next symbol

* $(1)$ &emsp;$S\Rightarrow E\Rightarrow (S)\Rightarrow (E)\Rightarrow (1)$
* $(1)+2$ &emsp; $S\Rightarrow E+S\Rightarrow (S)+S\Rightarrow (E)+S\Rightarrow (1)+E\Rightarrow (1)+2$

因为每次只能向前看一个字符，所以很难去做选择

<br>

### A. The Basic Method of LL(1) Parsing

$S\rightarrow (S)S\vert \varepsilon$

<img src="../../../assets/images/image-20200321204904747.png" alt="image-20200321204904747" style="zoom:30%;" />

Parsing这一列是stack, $代表栈底

Input是要分析的目标串，$代表end of input

第一步,`S`是start symbol, action代表这一步采取的动作。`S`从stack中pop, 然后将`(S)S` push进stack, 进入的顺序是`S)S(`

当栈顶是terminal的时候，执行match操作，nonterminal则要使用一个产生式规则

<br>

`The two actions`{:.error}

`Generate`{:.success}: replace a non-termal A at the top of the stack by a string $\alpha$ (in reverse) using a grammar rule $A\rightarrow \alpha$

`Match`{:.success} : match a token on top of the stack with the next input token

<br>

The list of generating actiosn in the above table:

$S\Rightarrow (S)S\quad [S\rightarrow(S)S]$

$\Rightarrow ()S\quad[S\rightarrow \varepsilon]$

$\Rightarrow () \quad [S\rightarrow \varepsilon]$

{:.success}

Which corresponds to the steps in a **leftmost derivation** of string (). This is the **characteristic** of top-down parsing.

<br>

Constructing a parse tree:

Adding node constructin **actions** as each non-terminal or terminal is push onto the stack.

<br>

### B. The LL(1) Parsing Table and Algorithm

Purpose of the LL(1) parsing table:

&emsp;&emsp; To express the possible rule choices for a non-terminal A: A is at the top of parsing stack, based on the current input token(the look-ahead). 也就是根据parsing table来选择对应的分析表应该采取什么样的动作

<br>

N表示non-terminal , T表示terminal.

横向摆放的是terminal, $表示结束状态，纵向摆放non-terminal

如果表项中存在空项，表明一个潜在的错误

| M[N,T] | (                   | )                          | $                          |
| ------ | ------------------- | -------------------------- | -------------------------- |
| S      | $S\rightarrow (S)S$ | $S\rightarrow \varepsilon$ | $S\rightarrow \varepsilon$ |



<br>

`The table-constructing rule`{:.error}

* If $A\rightarrow \alpha$ is a production choice, and there is a derivation $\alpha\Rightarrow^*a\beta$, where `a` is a token ,then add $A\rightarrow\alpha$ to the table entry $M[A,a]$ 这条关注第一个terminal是什么 [first set]
* If $A\rightarrow \alpha$ is a production choice, and there are derivations $\alpha\Rightarrow^*\varepsilon$ and S\$$\Rightarrow^*\beta A\alpha\gamma $,  where S is the start symbol and a is a token(or \$), then add $A\rightarrow\alpha$ to the table entry $M[A,a]$. 这条说明如果非终结符可以为空的话，后面可以跟什么终结符 follow set

相当于构建前面后面讲的FIRST+ set

<img src="../../../assets/images/image-20200321214342763.png" alt="image-20200321214342763" style="zoom:40%;" />

<br>

`Definition of LL(1) Grammar`{:.error}

* A grammar is an LL(1) grammar  if the associated LL(1) parsing table has ***at most one*** production in each table entry
* An LL(1) grammar cannot be ***ambiguous***

<br>

A Parsing Algorithm Using the LL(1) Parsing Table:

```pascal
push the start symbol onto the top of the parsing stack
while the top of the parsing stack != $ and the next input token != $ do
	if the top of the parsing stack is terminal a and the next input token = a
	then (* match *)
		pop the parsing stack;
		advance the input;
	else if the top of the parsing stack is non-terminal A and the next input token is terminal a and parsing table entry M[A,a] contains production A->X1X2...Xn
	then (* generate *)
		pop the parsing stack;
		for i := n downto 1 do//倒序
			push Xi onto the parsing stack
	else error;

if the top of the parsing stack = $ and the next input token = $
then accept
else error.
```



<img src="../../../assets/images/image-20200321215232370.png" alt="image-20200321215232370" style="zoom:50%;" />

这个表存在问题，在else-part 行 else列，有两个产生式，违反了LL(1) grammar, 但是我们增加了消除歧义的规则. The  entry  M[else-part,  else]  contains  two  entries,  i.e.  the **dangling else ambiguity**

`Disambiguating  rule`{:.error}

always  prefer   the   rule   that generates the current look-ahead token over any other.

the production:  else-part → else statement preferred over the production  else-part →ε

![image-20200321215538080](../../../assets/images/image-20200321215538080.png)

<img src="../../../assets/images/image-20200321215559375.png" alt="image-20200321215559375" style="zoom:50%;" />

<br>

### C. Left Recursion Removal and Left Factoring

#### i. Left Recursion Removal

这两个是为EBNF的改造服务的

`左递归`{:.error}

{:.warning}

对于CFG中的一个规则来说，如果其右侧第一个符号与左侧符号相同或者能够推导出左侧符号，那么成为该规则是左递归的。前一种情况称为直接左递归，后一种情况称为间接左递归。

Immediate left recursion: the left recursion occurs only within the production of a single non-terminal.

&emsp;exp → exp + term $\vert $  exp - term $\vert$ term

Indirect left recursion: 

&emsp;A → Bb $\vert $ ...

&emsp;B → Aa $\vert $ ... 

<br>

##### Case 1: Simple immediate left recursion

&emsp;$A\rightarrow A\alpha\vert \beta$

Rewrite this grammar rule into two rules:

&emsp; $A\rightarrow \beta A'$

&emsp; $A'\rightarrow \alpha A'\vert \varepsilon$

左递归-->右递归

> Example:
>
> $exp\rightarrow exp\; addop\;term\vert term$
>
> 
>
> $exp\rightarrow term \;exp'$
>
> $exp'\rightarrow addop\;term \;exp'\vert \varepsilon$

##### Case2: General immediate left recursion

&emsp;$A\rightarrow A\alpha_1\vert A\alpha_2\vert\cdots \vert A\alpha_n\vert \beta_1\vert\beta_2\vert\cdots\vert\beta_m$

Where none of $\beta_1,\cdots,\beta_m$ begin with A. The solution is similiar to the simple case

Rewrite this grammar rule into two rules:

&emsp; $A\rightarrow \beta_1 A'\vert \beta_2 A'\vert\cdots\vert\beta_m A'$

&emsp; $A'\rightarrow \alpha_1 A'\vert\alpha_2 A'\vert\cdots\vert\alpha_nA'\vert \varepsilon$



##### Case3: General left recursion

Grammars with no $\varepsilon$-productions and no cycles.

* A cycle is a derivation of at least one step the begins and ends with same non-terminal: $$A\Rightarrow\alpha\Rightarrow^* A$$
* Programming language grammars do have $\varepsilon$-productions, but usually in very restricted forms.

Algorithm

算法为非终结符强制规定一种任意的顺序。外层循环按该顺序遍历非终结符。内层循环寻找任何满足下述条件的产生式：将Ai扩展为Aj开头的右侧句型，其中j < i。这种扩展可能导致间接左递归。

为了避免这种情况，该算法使用Aj相匹配的所有可能的产生式，将出现的每个Aj替换为相应产生式的右侧。即如果内层循环发现一个产生式$A_i\rightarrow A_j\gamma$, 且$A_j\rightarrow \delta_1\vert\delta_2\vert\cdots\vert\delta_k$, 那么算法会将$A_i\rightarrow A_j\gamma$替换为一组产生式$A_i\rightarrow\delta_1\gamma\vert\delta_2\gamma\vert\cdots\vert\delta_k\gamma$

```pascal
impose an order on the nonterminals A1, A2, ..., An
for i := 1 to m do
		for j := 1 to i - 1 do 
				replace each grammar rule choice of the form  Ai->AjB by the rule Ai->a1B|a2B|...|akB, where Aj->a1|a2|...|ak is the current rule for Aj
				remove, if necessary, immediate left recursion involving Ai
		end
		
		//消除直接左递归
		rewrite the productions to eliminate any direct left recursion on Ai
end
```

> Example:
>
> $A\rightarrow Ba\vert Aa\vert c$
>
> $B\rightarrow Bb\vert Ab\vert d$
>
> Where A1=A, A2=B, and n = 2
>
> 1.When i = 1, the inner loop does not execute, so only to remove the immediate left recursion of A
>
> $A\rightarrow BaA'\vert cA'$
>
> $A'\rightarrow aA'\vert\varepsilon$
>
> $B\rightarrow Bb\vert Ab\vert d$
>
> 2.When i = 2, the inner loop execute once, with j = 1.
>
> To eliminate the rule $B\rightarrow Ab$ by replacing A with its choices
>
> $A\rightarrow BaA'\vert cA'$
>
> $A'\rightarrow aA'\vert\varepsilon$
>
> $B\rightarrow Bb\vert BaA'b\vert cA'b\vert d$
>
> 3.remove the immediate left recursion
>
> $A\rightarrow BaA'\vert cA'$
>
> $A'\rightarrow aA'\vert\varepsilon$
>
> $B\rightarrow cA'bB'\vert dB'$
>
> $B'\rightarrow bB'\vert aA'bB'\vert\varepsilon$
>
> * Now, the grammar has no left recursion

Left recursion removal not changes the language, but change the grammar and the parse tree.

The change causes a complication for the parser.

<br>

#### ii. Left Factoring

Left factoring is required when two or more grammar rule choices share a common prefix string.

因为对于LL(1)来说，只看一个符号并不能从$\alpha\beta$和$\alpha\gamma$中做出选择。用提取左公因子的方法可以延迟选择

$$A\rightarrow\alpha\beta\vert\alpha\gamma\Rightarrow\begin{aligned}A&\rightarrow\alpha A'\\A'&\rightarrow\beta\vert\gamma\end{aligned}$$

Algorithm

```
while there are changes to the grammar do 
	for each non-terminal A do
		Let a be a prefix of maximal length that is shared by two or more production choices for A
		If a != epsilon then 
			Let A->a1|a2|...|an be all the production choices for A
			And suppose that a1, a2, ..., ak share a, so that A->ab1|ab2|...|abk|ak+1|...|an, the bj's share no common prefix, and ak+1,...an do not share a
			
			A->aA'|aK+1|...|an
			A'->b1|b2|...|bk
```



# 3. FIRST and FOLLOW set

### A. FIRST set

`First Set`{:.error}

> 对于语法符号 $\alpha$, $FIRST(\alpha )$是从 $\alpha$推导出的语句开头可能出现的终结符的集合。EOF隐含地出现在语法中每个语句的末尾，因而，它同时出现在FIRST的定义域和值域中。

Let $X$ be a grammar symbol(a terminal or non-terminal) or $\varepsilon$. Then $FIRST(X)$ is a set of terminals or $\varepsilon$, which is defined as follows.

1. If $X$ is a terminal or $\varepsilon$, then $FIRST(X)=\{X\}$;
2. If $X$ is a non-terminal, then for each production choice $X\rightarrow X_1X_2\cdots X_n$, $FIRST(X)$ contains $FIRST(X_1)-\{\varepsilon\}$

继续考虑2的情况

Let $ \alpha=X_1X_2\cdots X_n$ be a string of terminals and non-terminals, First($\alpha$) is defined as follows:

* $First(\alpha)$ contains $First(X_1)-\{\varepsilon\}$
* For each $i=2,\cdots,n$, if for all $k=1,\cdots ,i-1$, $First(X_k)$ contains $\varepsilon$, then $First(\alpha)$ contains $First(X_{k+1})-\{\varepsilon\}$
* If all the set $First(X_1),\cdots,First(X_n)$ contains $\varepsilon$, then $First(\alpha)$ contains $\varepsilon$

<br>

<img src="../../../assets/images/image-20200327111130047.png" alt="image-20200327111130047" style="zoom:50%;" />

<img src="../../../assets/images/image-20200327111314528.png" alt="image-20200327111314528" style="zoom:50%;" />



`Algorithm: `{:.error}

for each $ \alpha\in(T\cup EOF\cup\varepsilon)$ do: 	//对于终结符和EOF和$\varepsilon$, FIRST set就是本身

&emsp;FIRST($\alpha$)$ \leftarrow\alpha$

end;

for each $A\in NT$ do:		//将非终结符的First set清空

&emsp;FIRST(A)$ \leftarrow\varnothing$

end;

while(FIRST sets are still changing) do:

&emsp;for each $p\in P$, where p has the from $A\rightarrow X $ do:

&emsp;&emsp;if $X $ is $X _1X _2\cdots X_n$, where $X _i\in T\cup NT$, then begin:

&emsp;&emsp;&emsp;$rhs\leftarrow FIRST(X_1)-\{\varepsilon\}$

&emsp;&emsp;&emsp;$i\leftarrow 1$

&emsp;&emsp;&emsp;while($\varepsilon\in FIRST(X_i)$ and $i\leq n-1$)  do:

&emsp;&emsp;&emsp;&emsp;$rhs\leftarrow rhs\cup (FIRST(X_{i+1})-\{\varepsilon\})$

&emsp;&emsp;&emsp;&emsp;$i\leftarrow i+1$

&emsp;&emsp;&emsp;end;

&emsp;&emsp;end;

&emsp;&emsp;if $i=n$ and $\varepsilon\in FIRST(X_n)$

&emsp;&emsp;&emsp;then $rhs\leftarrow rhs\cup\{\varepsilon\}$

&emsp;&emsp;$FIRST(A)\leftarrow FIRST(A)\cup rhs$

&emsp;end;

end;

<br>

`Definition`{:.warning}

&emsp; A non-terminal A is ***nullable*** if there exists a derivation $A\Rightarrow^*\varepsilon$

`Theorem`{:.error}

&emsp; A non-terminal A is ***nullable*** if and only if $First(A)$ contains $\varepsilon$.

<br>

<img src="../../../assets/images/image-20200327124232470.png" alt="image-20200327124232470" style="zoom:50%;" />

看上图例子，可以根据look ahead symbol和FIRST set在规则2,3,4中做出选择。

如果look ahead symbol是+，语法分析器使用规则2进行扩展；如果是-, 使用规则3

但是规则4，会产生一些问题。FIRST($\varepsilon$)是$\{\varepsilon\}$, 无法匹配分析器返回的任何单词。直观看来，在look ahead symbol不是任何其他备选产生式的FIRST set的成员时，语法分析器应该应用$\varepsilon$产生式。为区分合法输入和语法错误，语法分析器必须知道在正确地应用了规则4之后，哪些单词可能作为第一个符号出现。因此提出了FOLLOW set

<br>

### B. FOLLOW set

`Follow Set`{:.error}

> 对于非终结符$\alpha$, Follow($\alpha$)是在语句中紧接$\alpha$出现的单词的集合

Given a non-terminal A, the set Follow(A):

1. if A is the start symbol, the \$ (represent EOF) is in the Follow(A)
2. if there is a production $B\rightarrow\alpha A\gamma$, then $First(\gamma)-\{\varepsilon\}$ is in $Follow(A)$ [和计算FIRST set不同，计算FOLLOW set要看产生式右边有A的情况，然后计算A后面式子的FIRST set]
3. if there is a production $B\rightarrow\alpha A\gamma$, such that $\varepsilon$ in $First(\gamma)$, then $Follow(A)$ contains $Follow(B)$.[因为$\gamma$可以为$\varepsilon$, 所以有$B\rightarrow\alpha A$, 因此可以跟在A后面的符号也一定可以跟在B(=$\alpha A$) 后面]

{:.warning}

Follow sets are defined only for non-terminal. The empty "pesudotoken" $\varepsilon$ is never an element of a follow set.

<br>

`Algorithm:`{:.error}

Follow(start-symbol) := {\$};

for all non-terminals $A\not=$ start-symbol do:

&emsp;Follow(A) := $\varnothing$;

while there changes to any Follow sets do:

&emsp; for each production $A\rightarrow X_1X_2\cdots X_n$ do:

&emsp;&emsp; for each $X_i$ that is a non-terminal do:

&emsp;&emsp;&emsp;add First($X_{i+1}X_{i+2}\cdots X_n$)-$\{\varepsilon\}$ to Follow($X_i$);	//规则2

&emsp;&emsp;&emsp;if $\varepsilon$ is in First($X_{i+1}X_{i+2}\cdots X_n$) then:

&emsp;&emsp;&emsp;&emsp;add Follow(A) to Follow($X_i$);							//规则3

<br>

<img src="../../../assets/images/image-20200327131725193.png" alt="image-20200327131725193" style="zoom:50%;" />

在语法分析器试图扩展Expr'时。如果look ahead symbol是+，语法分析器使用规则2进行扩展；如果是-, 使用规则3。如果前瞻符号在FOLLOW(Expr')中，则应用规则4。任何其他符号都会导致语法错误。

<br>

***Example***：

$S\rightarrow ES'$

$S'\rightarrow\varepsilon\vert +S$

$E\rightarrow num\vert (S)$

<br>

* 首先将产生式分解开来

$S\rightarrow ES'$

$S'\rightarrow\varepsilon$

$S'\rightarrow +S$

$E\rightarrow num$

$E\rightarrow (S)$

* 判定Nullable: Only S' is nullable

* FIRST

$First(ES')=\{num,(\}$

$First(+S)=\{+\}$

$First(num)=\{num\}$

$First((S))=\{(\}$

$First(S')=\{+,\varepsilon\}$

$First(S)=\{num,(\}$

* FOLLOW

$Follow(S)=\{),\$\}$

$Follow(S')=\{),\$\}$

$Follow(E)=\{+,),\$\}$

<br>

<br>

使用FIRST和FOLLOW集合，我们可以准确地规定使得某个语法对自顶向下语法分析器无回溯的条件。对于产生式$A\rightarrow\beta$, 定义其增强FIRST集合FIRST+

$$FIRST^+(A\rightarrow \beta)=\begin{aligned}&FIRST(\beta)\quad if\;\varepsilon\notin FIRST(\beta)\\ &FIRST(\beta)\cup FOLLOW(A)\end{aligned}$$

<br>



### Constructing LL(1) Parsing Tables

The following algorithmic construction of the LL(1) parsing table:

Repreat the following 2 steps for each non-terminal A and production choice $A\rightarrow\alpha$

1. For each token a in $First(\alpha)$, add $A\rightarrow\alpha$ to the entry M[A,a]
2. If $\varepsilon$ is in $First(\alpha)$, for each element a of $Follow(A)$ (a token or \$\), add $A\rightarrow\alpha$ to M[A,a]

这相当于

$$FIRST^+(A\rightarrow \beta)=\begin{aligned}&FIRST(\beta)\quad if\;\varepsilon\notin FIRST(\beta)\\ &FIRST(\beta)\cup FOLLOW(A)\end{aligned}$$

<br>

如何判定LL(1)语法

`Theorem`{:.error}

A grammar in BNF is LL(1) if the following conditions are satisfied.

1. For every production $A\rightarrow\alpha_1\vert\alpha_2\vert\cdots\vert\alpha_n$, $First(\alpha_i)\cap First(\alpha_j)$ is empty for all i and j, $1\leq i,j\leq n,i\not=j$ (否则在$First(\alpha_i)$这个格子里既可以写$A\rightarrow \alpha_i$也可以写$A\rightarrow\alpha_j$)
2. For every non-terminal A such that $First(A)$ contains $\varepsilon$, $First(A)\cap Follow(A)$ is empty. (否则在这个格子里既可以写$A\rightarrow \alpha$,也可以写$A\rightarrow\epsilon$)

<img src="../../../assets/images/image-20200328164147883.png" alt="image-20200328164147883" style="zoom:50%;" />



# Error Recovery in Top-Down Parsers

Different levels of response to errors:

> Give a meaningful error message;
>
> &emsp; Determine as closely as possible the ***location*** where that error has occured.
>
> &emsp; Some form of ***error correction***;(error repair)
>
> &emsp; The parser attempts to ***infer*** a correct program from the incorrect one given.

Most of the techniques for error recovery are ad hoc, and general principles are hard to come by.

<br>

Some important considerations taht apply are the following:

1. To determine that an error has occurred as soon as possible.
2. After an error has occurred

&emsp; pick a likely place to resume the parse

&emsp;try to parse as much of the code as possible

3. To avoid the error ***cascade*** problem
4. To avoid ***infinite loop***s on error

<br>

Some goals conflict with each other, so that a compiler writer is forced to make trade-offs during the construction of an error handler:

`Panic Mode`{:.error}

> A standard form of error recovery in recursive-decent parsers.
>
> The error handler will ***consume*** a possibly large number of tokens in an attempt to find a place to resume parsing.

The basic mechanism of panic mode:

* A set of ***synchronizing tokens***  are provided to each recursive procedure[非终结符];
* If an error is encountered, the parser scans ahead, throwing away tokens until one of the synchronized tokens is seen in the input, and then parsing is resumed.也就是说当错误发生时，就一直scan去寻找同步符号

The important decisions to be made in this error recovery method:

1. What tokens to add to the synchronizing set at each point in the parse?

&emsp;&emsp; Follow sets are important candidates[为什么呢？试想遇到一个if语句，知道他的Follow set后就知道后面可以跟着什么了，然后可以将中间不在Follow set中的不合法符号给扔掉，直到遇到Follow set中的符号]

2. First sets:

&emsp;&emsp;Prevent the error handler from skipping important tokens that begin major new constructs[在error handle的过程中，会consume一些token, 但是需要注意的是：不能把很重要的token consume掉，比如for,while, 扔掉这些token会影响后面的语句]

&emsp;&emsp;Detect errors early in the parse



`check-input`{:.error} procedure: performs the early look-ahead checking

```pascal
//检查token是否在first set里面；如果不在就consume一些token直到遇到first set或者follow set中的token
procedure checkinput(Firstset, Followset)
begin
	if not (token in firstset) then
		error;
		scanto(first ∪ followset)
	endif;
end
```

`scanto`{:.error} procedure:

```pascal
//consume一些token直到遇到synchronization set里面的元素
procedure scanto(synchset)
begin
	while not (token in synchset ∪ {$}) do
		gettoken;
end scanto
```

来看一下递归下降分析程序+错误处理后的代码

<img src="../../../assets/images/image-20200328172021098.png" alt="image-20200328172021098" style="zoom:50%;" />

```pascal
procedure expr(synchset)
begin
	checkinput({(,number}, synchset);
	if not (token in synchset) then
		term(synchset);
		while token = + or token = - do
			match(token);
			term(synchset);
		end while
    checkinput(synchset, {(,number});
   end if;
end exp;
```











