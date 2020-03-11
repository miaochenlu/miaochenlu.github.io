# Introduction

## The translation process

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302224246137.png" alt="image-20200302224246137" style="zoom:50%;" />



## 1. Scanner

## 2. parser

```
Determine the structure of the program
Input: the forms of tokens
Output: a parse tree or a syntax tree
```



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302230057554.png" alt="image-20200302230057554" style="zoom:50%;" />



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302230238663.png" alt="image-20200302230238663" style="zoom:50%;" />



## 3. The semantic analyzer

* static semantics: including **declarations** and **type checking**

* dynamic semantics

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200302230427498.png" alt="image-20200302230427498" style="zoom:50%;" />

## 4. The source code optimizer

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302230913321.png" alt="image-20200302230913321" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302231003291.png" alt="image-20200302231003291" style="zoom:50%;" />



## 5. code generator

* Input: intermediate code or IR
* Output: machine code, code for target machine

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302231027623.png" alt="image-20200302231027623" style="zoom:50%;" />

## 6. target code optimizer

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302231126451.png" alt="image-20200302231126451" style="zoom:50%;" />





### Major data structures in a compiler

* <u>Token</u>: a value of an enumerated data type the sets of tokens
* <u>syntax tree</u>: each node is a record whose fields represent the information collected by the parser and semantic analyzer
* <u>symbol table</u>: information associated with identifiers: **functions, variables, constants, and data types.**
* <u>literal table</u>: store **constants and strings** 
* <u>intermediate code</u>: this code kept as an array of text strings, a temporary text file, or as a linked list  of  structures.
* <u>temporary files</u>: using temporary files to hold  the products of intermediate steps

可以将编译分成两个阶段

> 1. analysis: lexical analysis, syntax analysis, semantics analysis(optimization)
> 2. synthesis: code-generaion(optimization)

Front end and back end

> * the front end: the scanner, parser, semantic analyser, intermediate code synthesis
>
> * the back end: the code generator, some optimization