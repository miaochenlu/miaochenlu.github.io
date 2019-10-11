---
title: "Morpheus"

tags: hardware
key: page-morpheus

---

# Morpheus: A Vulnerability-Tolerant Secure Architecture Based on Ensembles of Moving Target Defenses with Churn



<a href="https://www.researchgate.net/publication/332218484_Morpheus_A_Vulnerability-Tolerant_Secure_Architecture_Based_on_Ensembles_of_Moving_Target_Defenses_with_Churn">Morpheus</a>论文解读



# The Morpheus Secure Architecture

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191009222750332.png" alt="image-20191009222750332" style="zoom:50%;" />

# 1.Precise Runtime Domain Tagging

### Why does Morpheus use tags?

> Track the domin of each memory object during execution. 

### When will tags be used?

>  The churn unit will use the tags to re-randomize values at runtime.
>
> The attack detector use the tags to identify operations indicative of undefined semantics.



Morpheus tracks four distinct domains using 2-bit domain tags:

> code(C)
>
> Code pointers(CP)
>
> data pointers(DP)
>
> data(D)



### How to generate tags?

The Morpheus LLVM-based compiler extensions will generate initial tags.



#### Hardware support for tags

add tag bits







references:  

