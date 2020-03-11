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







