# Bottom-Up Parsing

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200402232034726.png" alt="image-20200402232034726" style="zoom:50%;" />

# 1. Overview of Bottom-Up Parsing

A bottom-up parser uses an ***explicit stack*** to perform a parse

The parsing stack will contain both tokens and nonterminals.

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200402233338618.png" alt="image-20200402233338618" style="zoom:50%;" />

* **Parsing actions**: a sequence of **shift** and **reduce** operations
* **Parsing state**: a stack of terminals and non-terminals(grows to the right)
* **Current derivation step** = always stack + input

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200402233459512.png" alt="image-20200402233459512" style="zoom:40%;" />

***Shift***: shift a terminal from the front of the input to the top of the stack

***Reduce***: Reduce a string $\alpha$ at the top of the stack to a nonterminal A, given the BNF choice $A\rightarrow\alpha$

A further feature of bottom-up parsers: grammars are always augumented with a ***new start symbol***. If S is the start symbol, a new start symbol S' is added to the grammar: $S'\rightarrow S$



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200402233904569.png" alt="image-20200402233904569" style="zoom:50%;" />

* Right Sentential Form:

  * Each of the intermediate strings of terminals and nonterminals in such a derivation is called a right sentential form.
  * Each such sentential form is **split** between the parsing stack and the input during a shift-reduce parse.

  

* viable prefix

  * The sequence of symbols on the parsing stack is called a viable prefix of the right sentential form. E, E+, E+n are all viable prefixes of the right sentential form E+n

    