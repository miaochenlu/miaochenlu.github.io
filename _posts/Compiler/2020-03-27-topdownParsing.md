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

### A. The idea of Recursive-Descent Parsing

$A\rightarrow\alpha$

* The grammar rule for a non-terminal A as a <u>definition for a procedure</u> to recognize an A.  可以看作非终结符的一个定义，A是定义对象，$\alpha$是定义内容

* The right-hand side of the grammar for A specifies the structure of the code for this procedure. (A是函数名，$\alpha$是函数体)

手写文法分析器的常用手段，实现方式简单。

<br>

> Example: the expression Grammar
>
> expr→ expr addop term|term
>
> addop → + -
>
> term→ term mulop factor | factor
>
> mulop → *
>
> factor → (expr) | number

```pascal
Procedure factor
BEGIN
  case token of
  (: 						//case '('
  	match((); 	//match '(' , true->getNextToken
    expr;				//函数调用，无参数
    match());		//match ')', 
  number:				//case number
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

从Expr开始，试图匹配a, 他应用规则1，在语法分析树的下边缘创建句型$Expr+Term$。现在，语法分析器再次面临非终结符Expr和输入单词a。 如果选择是一致的，他应该应用规则1，用$Expr+Term$替换Expr。不断重复

之所以会出现这个问题，是因为语法在产生式中使用了左递归。在使用左递归的情况下，自顶向下语法分析器可能会无限循环，而不会生成与输入匹配的起始终结符(也不会前移输入位置)。

> Example
>
> `If-stmt → if ( exp ) statement | if ( exp ) statement else statement`
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

**Q**: whether the **left associativity** implied by the curly bracket(花括号，循环) (and explicit in the original BNF) can still be maintained within this code.

<br>

A recursive-descent calculator for the simple integer arithmetic of our grammar:

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

# 2. LL(1) Parsing

最左匹配的自顶向下语法分析器中，低效的主要原因是回溯。如果语法分析器用错误的产生式扩展语法分析树的下边缘，在语法分析树的下边缘与词法分析器返回的单词之间，最终会出现不匹配的情况。在语法分析器发现这种不匹配情形时，它必须撤销构建出错误的语法分析树下边缘的操作，并尝试产生其他产生式。扩展，收缩，再扩展语法分析树下边缘的操作费时费力。

如果语法分析器在选择下一条规则时，可以同时考虑当前关注的符号以及下一个输入符号(lookahead symbol)，可以消除在解析右递归表达式语法时多种选择造成的不确定性。因而，我们说该语法在look ahead 1个符号时是无回溯的。无回溯语法也称预测性语法。

`First Set`{:.error}

> 对于语法符号 $\alpha$, FIRST($\alpha$ )是从 $\alpha$推导出的语句开头可能出现的终结符的集合。EOF隐含地出现在语法中每个语句的末尾，因而，它同时出现在FIRST的定义域和值域中。

<img src="../../../assets/images/image-20200327111130047.png" alt="image-20200327111130047" style="zoom:50%;" />

<img src="../../../assets/images/image-20200327111314528.png" alt="image-20200327111314528" style="zoom:50%;" />

我们已经对单个语法符号定义了FIRST set, 将该定义扩展到符号串也是很方便的。对于符号串$s=\beta_1\beta_2\cdots\beta_k$, 假设$\beta_n$是FIRST集合中不包含$\varepsilon$的第一个符号，我们定义FIRST(s)为$\beta_1,\beta_2,\cdots,\beta_n$的FIRST集合的并集，而且$\varepsilon\in FIRST(s)$到充分必要条件是：对于$1\leq i\leq k$, 都有$\epsilon\in\beta_i$

<br>

for each $\alpha\in(T\cup EOF\cup\varepsilon)$ do: 	//对于终结符和EOF和$\varepsilon$, FIRST set就是本身

​	FIRST($\alpha$)$\leftarrow\alpha$

end;

for each $A\in NT$ do:		//将非终结符的First set清空

​	FIRST(A)$\leftarrow\varnothing$

end;

while(FIRST sets are still changing) do:

​	for each $p\in P$, where p has the from $A\rightarrow\beta$ do:

​		if $\beta$ is $\beta_1\beta_2\cdots\beta_k$, where $\beta_i\in T\cup NT$, then begin:

​			$rhs\leftarrow FIRST(\beta_1)-\{\varepsilon\}$

​			$i\leftarrow 1$

​			while($\varepsilon\in FIRST(\beta_i)$ and $i\leq k-1$)  do:

​				$rhs\leftarrow rhs\cup (FIRST(\beta_{i+1})-\{\varepsilon\})$

​				$i\leftarrow i+1$

​			end;

​		end;

​		if $i=k$ and $\varepsilon\in FIRST(\beta_k)$

​			then $rhs\leftarrow rhs\cup\{\varepsilon\}$

​		$FIRST(A)\leftarrow FIRST(A)\cup rhs$

​	end;

end;

<br>

<img src="../../../assets/images/image-20200327124232470.png" alt="image-20200327124232470" style="zoom:50%;" />

看上图例子，可以根据look ahead symbol和FIRST set在规则2,3,4中做出选择。

如果look ahead symbol是+，语法分析器使用规则2进行扩展；如果是-, 使用规则3

但是规则4，会产生一些问题。FIRST($\varepsilon$)是$\{\varepsilon\}$, 无法匹配分析器返回的任何单词。直观看来，在look ahead symbol不是任何其他备选产生式的FIRST set的成员时，语法分析器应该应用$\varepsilon$产生式。为区分合法输入和语法错误，语法分析器必须知道在正确地应用了规则4之后，哪些单词可能作为第一个符号出现。因此提出了FOLLOW set



`Follow`{:.error}

> 对于非终结符$\alpha$, Follow($\alpha$)是在语句中紧接$\alpha$出现的单词的集合



first L--> from left to right

second L--> leftmost derivation

(1)-->look ahead 1 token(往前看的越多，能力越强，但是效率会低)

<br>

LL(1) Parsing uses an **explicit stack** rather than recursive calls to perform a parse;[recursive descend parsing 用的是recursive, 可以用stack来代替]

> Example:
>
> $S\rightarrow E+S|E$
>
> $E\rightarrow num|(S)$

<img src="../../../assets/images/image-20200321204355184.png" alt="image-20200321204355184" style="zoom:30%;" />

Want to decide which production to apply based on next symbol

* $(1)$ &emsp;$S\Rightarrow E\Rightarrow (S)\Rightarrow (E)\Rightarrow (1)$
* $(1)+2$ &emsp; $S\Rightarrow E+S\Rightarrow (S)+S\Rightarrow (E)+S\Rightarrow (1)+E\Rightarrow (1)+2$

因为每次只能向前看一个字符，所以很难去做选择



### A. The Basic Method of LL(1) Parsing

$S\rightarrow (S)S\vert \varepsilon$

<img src="../../../assets/images/image-20200321204904747.png" alt="image-20200321204904747" style="zoom:30%;" />

Parsing这一列是stack, $代表栈底的意思

Input是要分析的目标串，$代表end of input

第一步,S是start symbol, action代表这一步采取的动作。S从stack中pop, 然后将(S)S push进stack, 进入的顺序是S)S(

当栈顶是终结符的时候，执行match操作，非终结符则要使用一个产生式规则

> The two actions:
>
> * Generate: replace a non-termal A at the top of the stack by a string $\alpha$ (in reverse) using a grammar rule $A\rightarrow \alpha$
> * Match: match a token on top of the stack with the next input token

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

&emsp; To express the possible rule choices for a non-terminal A: A is at the top of parsing stack, based on the current input token(the look-ahead). 也就是根据parsing table来选择对应的分析表应该采取什么样的动作

<br>

N表示non-terminal , T表示terminal.

横向摆放的是终结符, $表示结束状态，纵向摆放非终结符

如果表项中存在空项，表明一个潜在的错误

| M[N,T] | (                   | )                          | $                          |
| ------ | ------------------- | -------------------------- | -------------------------- |
| S      | $S\rightarrow (S)S$ | $S\rightarrow \varepsilon$ | $S\rightarrow \varepsilon$ |



<br>

The table-constructing rule:

* If $A\rightarrow \alpha$ is a production choice, and there is a derivation $\alpha\Rightarrow^*a\beta$, where `a` is a token ,then add $A\rightarrow\alpha$ to the table entry $M[A,a]$ 这条关注第一个终结符是什么 first set
* If $A\rightarrow \alpha$ is a production choice, and there are derivations $\alpha\Rightarrow^*\varepsilon$ and $S\$\Rightarrow^*\beta A\alpha\gamma $, where S is the start symbol and a is a token(or \$), then add $A\rightarrow\alpha$ to the table entry $M[A,a]$. 这条说明如果非终结符可以为空的话，后面可以跟什么终结符 follow set

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200321214342763.png" alt="image-20200321214342763" style="zoom:40%;" />

<br>

Definition of LL(1) Grammar:

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

这个表存在问题，在else-part 行 else列，有两个产生式，违反了LL(1) grammar, 但是我们增加了消除歧义的规则

the  entry  M[else-part,  else]  contains  two  entries,  i.e.  the **dangling else ambiguity**

Disambiguating  rule:   always  prefer   the   rule   that generates the current look-ahead token over any other.

the production:  else-part → else statement preferred over the production  else-part →ε

![image-20200321215538080](../../../assets/images/image-20200321215538080.png)

<img src="../../../assets/images/image-20200321215559375.png" alt="image-20200321215559375" style="zoom:50%;" />

<br>

### C. Left Recursion Removal and Left Factoring

#### i. Left Recursion Removal

这两个是为EBNF的改造服务的

`左递归`{:.error}

{:.warning}

对于CFG中的一个规则来说，如果其右侧第一鹅符号与左侧符号相同或者能够推导出左侧符号，那么成为该规则是左递归的。前一种情况称为直接左递归，后一种情况称为间接左递归。

Immediate left recursion: the left recursion occurs only within the production of a single non-terminal.

&emsp;exp → exp + term | exp - term |term

Indirect left recursion: 

&emsp;A → Bb |...

&emsp;B → Aa |... 

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
> 1. When i = 1, the inner loop does not execute, so only to remove the immediate left recursion of A
>
> $A\rightarrow BaA'\vert cA'$
>
> $A'\rightarrow aA'\vert\varepsilon$
>
> $B\rightarrow Bb\vert Ab\vert d$
>
> 2. When i = 2, the inner loop execute once, with j = 1.
>
> To eliminate the rule $B\rightarrow Ab$ by replacing A with its choices
>
> $A\rightarrow BaA'\vert cA'$
>
> $A'\rightarrow aA'\vert\varepsilon$
>
> $B\rightarrow Bb\vert BaA'b\vert cA'b\vert d$
>
> 3. remove the immediate left recursion
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

#### ii. Left Factoring

Left factoring is required when two or more grammar rule choices share a common prefix string.

因为对于LL(1)来说，只看一个符号并不能从$\alpha\beta$和$\alpha\gamma$中做出选择。用提取左公因子的方法可以延迟选择

$$A\rightarrow\alpha\beta\vert\alpha\gamma\Rightarrow\begin{aligned}A&\rightarrow\alpha A'\\A'&\rightarrow\beta\gamma\end{aligned}$$

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





