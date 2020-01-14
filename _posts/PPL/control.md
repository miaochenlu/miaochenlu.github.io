# Control

# 1. what is a control structure

> A control structure is ***a control statement*** and ***the statements whose execution it controls*** 



# 2. 4 types

## 2.1 Selection Statements

```cpp
if control_expression
then clause
else clause
```



## 2.2 Iterative Statements

> The repeated execution of a statement or compound statement is accomplished either by iteration or recursion 

```cpp
for ([expr_1]; [expr_2]; [expr_3])
  statement
```



## 2.3 unconditional branching

> Transfers execution control to a specified place in the program, e.g., `goto `



## 2.4 guarded commands

pursose:

> to support a new programming methodology that supports verification (correctness) during development

basic idea:

>  if the order of evaluation is not important, the program should not specify one

form

> ```cpp
> if <Boolean exp> -> <statement>
> []<Boolean exp> -> <statement>
> ...
> []<Boolean exp> -> <statement> 
> fi
> ```
>
> * Evaluate all Boolean expressions
>
> * If <boolean exp\> are true, choose one non-deterministically
>
> * If none are true, it is a runtime error
>
> * Program correctness cannot depend on statement chosen

see an example

```cpp
if x >= y -> max := x
[] y >= x -> max := y
fi
```

x=y的话，任选其中一个执行

```cpp
do <Boolean> -> <statement>
[]<Boolean> -> <statement>
...
[]<Boolean> -> <statement> 
od
```

* Evaluate all Boolean expressions

* If more than one are true, choose one non-deterministically; then start loop again

* If none are true, exit loop