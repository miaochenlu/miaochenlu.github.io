# Top-Down Parsing

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200316095402390.png" alt="image-20200316095402390" style="zoom:50%;" />

# 1. Top-Down Parsing by Recursive-Descent

### A. The idea of Recursive-Descent Parsing

$A\rightarrow\alpha$

* The grammar rule for a non-terminal A as a definition for a procedure to recognize an A

* The right-hand side of the grammar for A specifies the structure of the code for this procedure. (A是函数名，$\alpha$是函数体)

手写编译器的常用手段，实现方式简单。

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

It requires the use of EBNF.

## B. Repetition and Choice: Using EBNF

> Example
>
> `If-stmt → if ( exp ) statement | if ( exp ) statement else statement`
>
> The EBNF of the if-statement is as follows:
>
> `If-stmt → if ( exp ) statement [ else statement]`



Example: expr → expr addop term|term

&emsp;this would lead to an immediate ***infinite recursive loop***.

&emsp;both exp and term can begin with the same tokens (a number or left parenthesis).

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

<br>

**Q**: whether the **left associativity** implied by the curly bracket (and explicit in the original BNF) can still be maintained within this code.

<br>

A recursive-descent calculator for the simple integer arithmetic of our grammar:

```pascal
function exp : integer;
  var temp: integer;
  begin
    temp := term;
    while token = + or token = - do
      case token of
        + : match(+);
          temp := temp + term;
        -: match(-);
          temp := temp - term;
      end case;
    end while;
  return temp;
end exp;
```

### C. Further Decision Problems

The recursive-descent method is quite powerful and adequate to construct a complete parse. But we need more formal methods to deal with complex situation.

&emsp;1. It may be difficult to convert a grammar in `BNF`{:.warning} into `EBNF`{:.warning} form

&emsp;2. It is difficult to decide when to use the choice $A\rightarrow\alpha$ and the choice $A\rightarrow\beta$; if both $\alpha$ and $\beta$ begin with non-terminals. (<u>First Sets</u>)

&emsp;3. It may be necessary to know what token legally coming from the non-terminal A, $A\rightarrow\varepsilon$. (<u>Follow Sets</u>)

&emsp;4. It requires computing the <u>First and Follow</u> sets in order to detect the errors as early as possible.



# 2. LL(1) Parsing

first L--> from left to right

second L--> leftmost derivation

(1)-->look ahead 1 token



LL(1) Parsing uses an **explicit stack** rather than recursive calls to perform a parse;