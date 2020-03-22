# Top-Down Parsing

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200316095402390.png" alt="image-20200316095402390" style="zoom:50%;" />

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

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200321204355184.png" alt="image-20200321204355184" style="zoom:30%;" />

Want to decide which production to apply based on next symbol

* $(1)$ &emsp;$S\Rightarrow E\Rightarrow (S)\Rightarrow (E)\Rightarrow (1)$
* $(1)+2$ &emsp; $S\Rightarrow E+S\Rightarrow (S)+S\Rightarrow (E)+S\Rightarrow (1)+E\Rightarrow (1)+2$

因为每次只能向前看一个字符，所以很难去做选择



### A. The Basic Method of LL(1) Parsing

$S\rightarrow (S)S\vert \varepsilon$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200321204904747.png" alt="image-20200321204904747" style="zoom:30%;" />

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



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200321215232370.png" alt="image-20200321215232370" style="zoom:50%;" />

这个表存在问题，在else-part 行 else列，有两个产生式，违反了LL(1) grammar, 但是我们增加了消除歧义的规则

the  entry  M[else-part,  else]  contains  two  entries,  i.e.  the **dangling else ambiguity**

Disambiguating  rule:   always  prefer   the   rule   that generates the current look-ahead token over any other.

the production:  else-part → else statement preferred over the production  else-part →ε

![image-20200321215538080](/Users/jones/Library/Application Support/typora-user-images/image-20200321215538080.png)

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200321215559375.png" alt="image-20200321215559375" style="zoom:50%;" />

<br>

### C. Left Recursion Removal and Left Factoring

这两个是为EBNF的改造服务的

* Left Recursion removal

Immediate left recursion: the left recursion occurs only within the production of a single non-terminal.

&emsp;exp → exp + term | exp - term |term

Indirect left recursion: 

&emsp;A → Bb |...

&emsp;B → Aa |... 

<br>

#### Case 1: Simple immediate left recursion

&emsp;$A\rightarrow A\alpha\vert \beta$

Rewrite this grammar rule into two rules:

&emsp; $A\rightarrow \beta A'$

&emsp; $A'\rightarrow \alpha A'\vert \varepsilon$

> Example:
>
> $exp\rightarrow exp\; addop\;term\vert term$
>
> 
>
> $exp\rightarrow term \;exp'$
>
> $exp'\rightarrow addop\;term \;exp'\vert \varepsilon$

#### Case2: General immediate left recursion

&emsp;$A\rightarrow A\alpha_1\vert A\alpha_2\vert\cdots \vert A\alpha_n\vert \beta_1\vert\beta_2\vert\cdots\vert\beta_m$

Where none of $\beta_1,\cdots,\beta_m$ begin with A. The solution is similiar to the simple case

Rewrite this grammar rule into two rules:

&emsp; $A\rightarrow \beta_1 A'\vert \beta_2 A'\vert\cdots\vert\beta_m A'$

&emsp; $A'\rightarrow \alpha_1 A'\vert\alpha_2 A'\vert\cdots\vert\alpha_nA'\vert \varepsilon$



#### Case3: General left recursion



