---
layout: default
title:  "All solutions to Ax=b"
date:   2021-04-13
categories: math
parent: Linear Algebra
math: katex
---

# All of the solutions to Ax=b

It should be simple right? Well, there are 7 different possible cases, and only 3 have simple solutions. Lets summarize.

## Summary

We study

$$
Ax = b
$$

where $$A \in \mathbb{C}^{m\times n}$$, $$x \in \mathbb{C}^n$$ and $$b \in \mathbb{C}^{b}$$.

We will also use the adjoint $$A^* \in \mathbb{C}^{n \times m}$$ of $$A$$.

A few useful properties:
1. If, and only if, $$b \in R(A)$$, there exists a solution $$x$$.
2. If $$b \not \in R(A)$$ we are interested in finding a solution to $$b_r$$ which minimises the residual, $$\|Ax - b\|$$. 
3. If there are linearly independent columns, $$A$$ must be tall, and admits the left inverse $$A^+ = (A^* A)^{-1}A^*$$
4. If there are linearly independent rows, $$A$$ must be fat, and admits the right inverse $$A^+ = A^* (A A^*)^{-1}$$. 

|  | $$b \in R(A) \implies $$ **solution exists** | $$b\not \in R(A)\implies $$ solution **does not** exists |
|**indep. cols**: $$N(A) = \{0\}$$ (tall) | unique: $$x = A^+ b = (A^* A)^{-1} A^* b$$ ![](../assets/images/axb/case1.png) | least squares: $$\tilde x = A^+ b = (A^* A)^{-1} A^* b$$ ![](../assets/images/axb/case2.png)|
|**indep. rows**: $$N(A^*) = \{0\}$$ (fat)  | non-unique: $$x = x_r + x_n$$, and smallest sol: $$x_r = A^+ b = A^* (A A^*)^{-1} b$$ ![](../assets/images/axb/case3.png)| Impossible Case (by rank-nullity) |
|dep. cols: $$N(A) \neq \{0\}$$ (tall)  | solution not unique ![](../assets/images/axb/case4.png) | no solution ![](../assets/images/axb/case5.png)|
|dep. rows: $$N(A^*) \neq \{0\}$$ (fat)  | (same as above) solution not unique ![](../assets/images/axb/case4.png)| (same as above)  no solution ![](../assets/images/axb/case5.png)|


## More Details

In the most general case, we can split the solutions as follows:

$$
x = x_r + x_n\\
b = b_r + b_n
$$

where

$$
b_r \in R(A)\\
x_n \in N(A)\\
x_r \in R(A^*)\\
b_n \in N(A^*)
$$

then we have the following relations:

$$
A x_n = 0\\
A^* b_n = 0\\
$$

And so 

$$
Ax = b\\
A(x_r + x_n) = b_r + b_n\\
A x_r = b_r + b_n
$$

and so 

$$
A^* A x_r = A^*b_r \implies \text{ if } A^* A \text{ is invertible, }  \quad x_r = (A^* A )^{-1} A^* b, 
$$

