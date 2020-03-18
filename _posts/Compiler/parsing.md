# Context-Free Grammars and Parsing

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200310131928378.png" alt="image-20200310131928378" style="zoom:50%;" />



# 1. the parsing process

The parser can be viewed as a <font color=blue>function</font>

***input***: the sequence of tokens produced by the scanner

***output***: syntax tree



1. The parser calls a scanner procedure such as <font color=blue>getToken</font>(e.g. yylex) to fetch the next token from the input

2. In a single-pass compiler,  the parser will incorporate all the other phases of a compiler, <font color=blue>parse( )</font> ;

3. More commonly, a compiler will be **multi-pass** the further passes will use the syntax tree as their input.

<br>

### structure of syntax tree

* The structure of the syntax tree : dependent on the particular <u>syntactic structure of the language</u>

* This tree is usually defined as a dynamic data structure
  * each node consists of a record whose fields include the attributes needed for the remainder of the compilation process. 



### problem

One problem that is more difficult: the treatment of errors. 

1. Error in the <font color=red>scanner</font>

* Generate an error token and consume(delete) the offending character.

2. Error in the <font color=red>parser</font>

* Report an error message
* Recover from the error and continue parsing (to find as many errors as possible). 
* Sometimes, a parser may perform error repair

.



# 2. Context-free grammars(CFG)

regular expression是CFG的子集

* A  context-free  grammar  is  a  specification  for  the syntactic structure of a programming language. 

* A context-free grammar involves recursive rules.

$exp\rightarrow exp\;op\;exp|(exp)|number$

$op\rightarrow+|-|*$



A Context-Free Grammar is a 4-tuple $(V,\Sigma,S,\rightarrow)$

* V is a finite set of <font color=red>nonterminal</font> symbols
* $\Sigma$ is a finite set of <font color=blue>terminal</font> symbols
* $S\in V$ is a distinguished <font color=red>nonterminal</font>, the start symbol
* $\rightarrow\subseteq V\times(V\cup\Sigma)^*$ is a finite relation, the productions



Use <font color=red>UPPERCASE</font> letters for structure name(nonterminal) and <font color=blue>lowercase</font> letters for individual token symbols(terminals) 

$E\rightarrow E\;O\;E|(E)|n$

$O\rightarrow +|-|*$

<br>

Grammar: Let $G=(V,\Sigma,S,\rightarrow)$ be a CFG. The <font color=blue>directly derives</font> relation $\Rightarrow$ is defined as $\{(\alpha A\gamma, \alpha\beta\gamma)|A\rightarrow \beta\}$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200310134529652.png" alt="image-20200310134529652" style="zoom:50%;" />



<br>

* ***<font color=green>Derivation</font>***:   Grammar  rules  determine  the  legal  strings  of token symbols by means of derivations.

* A derivation  is a sequence of **replacements** of structure names by choices on the right-hand sides(RHS) of grammar rules. 

* A derivation begins with a single structure name and ends with **a string of token symbols**. 

* At each step in a derivation, a single replacement is made using one choice from a grammar rule.



<br>

Derivation steps use a different arrow from the arrow matasymbol in the grammer rules. 

$$L(G)=\{s|exp\Rightarrow^* s\}$$

$\Rightarrow^*$表示推导任意多次

推导就是用规则不断替换产生式的过程。如果符号全部是终结符，那么推导结束。

$L(G)$表示文法能产生的串的集合，即文法所能表达的语言

<br>



* G represents the expression grammar
* s represents an arbitrary string of token symboks(sometime called a sentence) 由终结符组成的串叫做sentence
* The symbol $s\Rightarrow^*$ stand for a derivation consisting of a sequence of replacements as described earlier
* Grammar rules are sometimes called productions because they "produce" the strings in $L(G)$ via derivations

<br>

<br>

* ***the start symbol***: the most general structure is listed first in the grammar rules.

*  ***nonterminals***:  structure names are also called nonterminals,  since  they  always  must  be  replaced  further  on  in  a derivation .

* ***terminals***: symbols  in  the  alphabet  are  called  terminals,  since  they  terminate  a  derivation.  Terminals  are  usually tokens in compiler applications. 

<br>

The grammar for a programming language often defines a structure called program.

Language of this structure is the set of all syntactically legal programs of the programming language

For example: a BNF for pascal language

&emsp; $program\rightarrow program-heading;program-block$

&emsp;$program-heading\rightarrow \cdots$

&emsp;$program-block\rightarrow\cdots$





> Example: Consider the following extremely simplified grammar of statements:
>
> $Statement\rightarrow if-stmt|\textbf{other}$
>
> $if-stmt\rightarrow \textbf{if}\; (exp)\; statement |\textbf{if}\;(exp)\;statement\;\textbf{else}\;statement$
>
> $exp\rightarrow 0|1$



`Recursion`{:.warning}: the grammar rule $A\rightarrow A\;a|a$ [left recursion]or the grammar rule $A\rightarrow a\; A|a$[right recursion]



Generates the language $\{a^n| n\; an\; integer\geq 1\}$  (the set of all strings of one or more a's)---> the regular expression $a^+$

**Left recurisive**: the nonterminal A appears as the first symbol on the right-hand side of the rule defining A.

**Right recurisive**: the nonterminal A appeawrs as the last symbol on the right-hand side of the rule defining A

<br>

Consider a rule of the form 

$$A\rightarrow A\;\alpha|\beta$$

where $\alpha$ and $\beta$ represent arbitrary strings and $\beta$ does not begin with A

This rule generates all strings of the form $\beta, \beta\alpha,\beta\alpha\alpha,\cdots\Rightarrow \beta\alpha^*$

$A\rightarrow \alpha\;A|\beta$ generates all strings $\beta,\alpha\beta,\alpha\alpha\beta,\cdots\Rightarrow\alpha^*\beta$

<br>

### epsilon production

To generate the same language as the regular expression $a^*$ we must have a notation for a grammar rule that generates the empty string(since regular expression $a^*$ matches the empty string).

$$empty\rightarrow $$

Use the epsilon metasymbol for the empty string(similar to its use in regular expressions):

$$empty\rightarrow\epsilon$$

Such a grammar rule is called an $\epsilon-$proguction. A grammar that generates a language containing the empty string must have at least one $\epsilon-$production.

<br>

A grammar that is equivalent to the regular expression $a^*$ as 

$$A\rightarrow A\;a|\epsilon\;\;or\;\; A\rightarrow a\;A|\epsilon$$

Both grammars generate the language $\{a^n|n\;an\;integer\geq0\}=L(a^*)$



> Example: 
>
> $Statement\rightarrow if-stmt|\textbf{other}$
>
> $if-stmt\rightarrow \textbf{if}\; (exp)\; statement\; else-part$
>
> $else-part\rightarrow \textbf{else}\;statement|\epsilon$
>
> $exp\rightarrow 0|1$
>
> the $\epsilon-$production indicates that the structure else-part is **optional**

<br>

> Example: $A\rightarrow (A)A|\epsilon$
>
> This grammar generates the strings fo all **balanced parentheses**.
>
> (() (())) ()
>
> $A\Rightarrow (A)A\Rightarrow (A)(A)A\Rightarrow (A)(A)\Rightarrow (A)()\Rightarrow ((A)A)()$
>
> $\Rightarrow (()A)()\Rightarrow (()(A)A)()\Rightarrow (()(A))()\Rightarrow (()((A)A))()\Rightarrow (()(()A))()\Rightarrow (()(()))() $
>
> 这个不能用正则表达式表示，因为它没有计数功能

<br>

> Example: Consider the following grammar G for a sequence of statements:
>
> &emsp; $stmt-sequence\rightarrow stmt;\;stmt-sequence|stmt$
>
> &emsp;$stmt\rightarrow s$
>
> This grammat generates sequences of one or more statements separated by semicolons
>
> $$L(G)=\{s,s;s,s;s;s,\cdots\}$$
>
> {:.warning}
>
>  semicolon ":" is statement separator
>
> If we want to allow statement sequences to also be empty, we could write the following grammar G'
>
> &emsp; $stmt-sequence\rightarrow stmt;stmt-sequence|\epsilon$
>
> &emsp;$stmt\rightarrow s$
>
> $$L(G')=\{\epsilon,s;, s;s;, s;s;s;, \cdots\}$$
>
> {:.warning}
>
> semicolon ":" is statement terminator
>
> <br>
>
> Allow statement sequences to be empty, but retain the semicolon as a statement separator
>
> &emsp; $stmt-sequence\rightarrow nonempty-stmt-sequence|\epsilon$
>
> &emsp;$nonempty-stmt-sequence\rightarrow stmt;nonempty-stmt-sequence|stmt$
>
> &emsp;$stmt\rightarrow s$

# 3. Parse trees and abstract syntax trees

Derivations do not uniquely represent the structure of the strings they construct. There are many derivations for the same string.

## A. Parse trees

A parse tree corresponding to a derivation is a labeled tree

* The interior nodes are labeled by <u>nonterminals</u>
* The leaf nodes are labeled by <u>terminals</u>
* The children of each internal node represent the replacement of the associated nonterminal in one step of the derivation

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315204307286.png" alt="image-20200315204307286" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315204335977.png" alt="image-20200315204335977" style="zoom:50%;" />

It  is  possible  to  distinguish  particular  derivations  that  are uniquely associated with the parse tree.

* A  left most  derivation:  a  derivation  in  which  the  leftmost nonterminal is replaced at each step in the derivation.
  * Corresponds to the **preorder** numbering of the internal nodes of its associated parse tree.

* A rightmost derivation: a derivation in which the rightmost nonterminal is replaced at each step in the derivation. 
  * Corresponds to the **postorder** numbering of the internal nodes of its associated parse tree. 



## B. Abstract syntax trees

抽象语法数去掉了分析数的不必要的细节

The principle of syntax-directed translation states that the meaning,  or  semantics,  of  the  string 3+4  should  be directly related to its syntactic structure as represented by the parse tree. 

The principle of syntax-directed translation means that the parse tree should imply that the value 3 and the value 4 are to be added. 

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315205013351.png" alt="image-20200315205013351" style="zoom:50%;" />

A  much  simpler  way  to  represent  this  same information, namely, as the tree

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315205101643.png" alt="image-20200315205101643" style="zoom:50%;" />



Abstract Syntax Trees, or Syntax Trees:

1. Such trees represent abstractions of the **actual** source code token sequences, and 

the token sequences cannot be recovered from them (unlike parse trees). 

2. A parse tree is a representation for the structure of ordinary called **concrete** syntax

when comparing it to abstract syntax.

3. Abstract syntax can be given a formal definition using a BNF-like notation, just like concrete syntax. 



Example:

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315205636991.png" alt="image-20200315205636991" style="zoom:50%;" />



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315205729399.png" alt="image-20200315205729399" style="zoom:50%;" />



A set of C declarations that would be appropriate for the structure of the statements and expressions in this example  is as follows: 

```cpp
typedef enum {ExpK, stmtK} NodeKind; //结点类型，表达式，语句
typedef enum {Zero, One} ExpKind;
typedef enum {IfK, OtherK} StmtKind;
typedef struct streenode {
  NodeKind kind;
  ExpKind ekind;
  StmtKind skind;
  struct streenode *test, *thenpart, *elsepart;
} StreeNode;
typedef StreeNode* SyntaxTree;
```

<br>

> Example: 
>
> $stmt-sequence\rightarrow stmt;stmt-sequence|stmt$
>
> $stmt\rightarrow s$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315211810958.png" alt="image-20200315211810958" style="zoom:50%;" />



To bind all the statement nodes in a sequence together with just one node 

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315211905014.png" alt="image-20200315211905014" style="zoom:50%;" />

The problem: a seq node may have an arbitrary number of children

Solution: use the standard leftmost-child right-sibling representation for a tree(presented in most data structures texts).

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315212022245.png" alt="image-20200315212022245" style="zoom:50%;" />



# 4. Ambiguous grammars

$exp\rightarrow exp\;op\;exp|(exp)|number$

$op\rightarrow +|-|*$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315212139589.png" alt="image-20200315212139589" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315212254005.png" alt="image-20200315212254005" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200315212406444.png" alt="image-20200315212406444" style="zoom:50%;" />

**An ambiguous grammar**:  a grammar that generates a string with ***more than one parse trees*** is called an ambiguous grammar. 

Such a grammar represents a serious problem for a parser. It does not specify precisely the syntactic structure of a program. 

<br>

Two basic method:

1. 

A rule: that **specifies** in each ambiguous case which of the parse trees (or syntax trees) is the correct one. 有歧义时指明选择哪一个分析树

Such a rule is called a **disambiguating rule**

* The advantage: it corrects the ambiguity without changing (and possibly complicating) the grammar. 

* The disadvantage: the syntactic structure of the language is no longer given by the grammar alone.给定文法之外还要给定一套规则

2.

Change the grammar into a form that **forces** the construction of the correct parse tree.可读性下降

<br>

To remove the given ambiguity in our simple expression grammar:

* State a disambiguating rule that establishes the relative precedences of the three operations represented. 

* The associativity of each of the operations of addition, subtraction, and multiplication. 







