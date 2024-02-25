---
layout: default
title:  "Inverse of Block Matrices"
date:   2024-02-25
categories: math
parent: Linear Algebra
math: katex
---
# Inverse of Block Matrices

The inverse of a partitioned matrix appears so often in research, I decided to collect some of the results into this document.  Most of these results come from

1. ["Inverses of 2x2 Block Matrices" by Tzon-Tzer Lu and Sheng-Hua Shiou](https://www.sciencedirect.com/science/article/pii/S0898122101002784)
2. ["The Matrix Cookbook" by Peterson and Pedersen](https://www.math.uwaterloo.ca/~hwolkowi/matrixcookbook.pdf).


## $$2 \times 2$$ matrices

Given an invertible matrix 

$$
A = \begin{bmatrix}
a & b\\
c & d
\end{bmatrix}
$$ 

the inverse is 

$$
A^{-1} = 
\frac{1}{ad - bc}
\begin{bmatrix}
d & -b\\
-c & a
\end{bmatrix}
= \frac{1}{\operatorname{det}(A)} \operatorname{adj}(A)
$$


## General Block Partitioned Matrices

Consider 

$$
P = \begin{bmatrix}
A & B \\
C & D
\end{bmatrix}
$$ 

where $$A, B, C, D$$ are each matrices of compatible sizes.

Then the inverse (if it exists) can be written as

$$
P^{-1} = \begin{bmatrix}
E & F \\
G & H
\end{bmatrix}
$$

We have different cases based on the invertibility of $$P$$. All sizes are assumed to be compatible.

__Theorem 1__: Assume $$A$$ is invertible. The matrix $$P$$ is invertible if and only if the schur complement of $$A$$, 

$$
X = D - C A^{-1} B = S(A)
$$

is invertible, and the inverse is

$$
P^{-1} = 
\begin{bmatrix}
A^{-1} + A^{-1} B X^{-1} C A^{-1} & 
-A^{-1} B X^{-1} \\ 
-X^{-1} C A^{-1} &
X^{-1}
\end{bmatrix}
$$

_Proof:_ See Theorem 2.1 of Lu and Shiou, "Inverses of 2x2 Block Matrices", 2000


__Theorem 2:__  Assume $$B$$ is invertible. The matrix $$P$$ is invertible if and only if the schur complement of $$B$$, 

$$
X = C - D B^{-1} A
$$ 

is invertible, and the inverse is 

$$
P^{-1} = 
\begin{bmatrix}
- X^{-1} D B^{-1} & X^{-1} \\
- B^{-1} + B^{-1} A X^{-1} D B^{-1} & 
- B^{-1} A X^{-1}
\end{bmatrix}
$$

_Proof:_ See Theorem 2.2 of Lu and Shiou, "Inverses of 2x2 Block Matrices", 2000

__Theorem 3:__  Assume $$C$$ is invertible. The matrix $$P$$ is invertible if and only if the schur complement of $$C$$, 

$$
X = B - A C^{-1} D
$$ 

is invertible, and the inverse is 

$$
P^{-1} = 
\begin{bmatrix}
-C^{-1} D X^{-1} &
C^{-1} + C^{-1} D X^{-1} A C^{-1}\\
X^{-1} & - X^{-1} A C^{-1}
\end{bmatrix}
$$

_Proof:_ See Theorem 2.2 of Lu and Shiou, "Inverses of 2x2 Block Matrices", 2000

__Theorem 4:__ Assume $$D$$ is invertible. The matrix $$P$$ is invertible if and only if the schur complement of $$D$$, 

$$
X = A - B D^{-1} C
$$

is invertible, and the inverse is 

$$
P^{-1} = 
\begin{bmatrix}
X^{-1} & - X^{-1} B D^{-1}\\
-D^{-1} C X^{-1} & D^{-1} + D^{-1} C X^{-1} B D^{-1}
\end{bmatrix}
$$


_Proof:_ See Theorem 2.1 of Lu and Shiou, "Inverses of 2x2 Block Matrices", 2000

----------------------------


We can use the above equations to derive some corollaries. These are repeated here, without proof. 

__Corollary 1:__ For invertible $$A, D$$, 

$$
P = \begin{bmatrix}
A & 0\\
0 & D 
\end{bmatrix}
\iff 
P^{-1} = \begin{bmatrix}
A^{-1} & 0 \\
0 & D^{-1}
\end{bmatrix}
$$

__Corollary 2:__ For invertible $$B, C$$, 

$$
P = \begin{bmatrix}
0 & B \\
C & 0 
\end{bmatrix}
\iff
P^{-1} = \begin{bmatrix}
0 & C^{-1}\\
B^{-1} & 0
\end{bmatrix}
$$

Theorem 2, 3, and corollary 2 are particularly useful in cases like

$$
P = \begin{bmatrix}
0 & 1 & 0 & 0\\
0 & 0 & 1 & 0\\
0 & 0 & 0 & 1\\
1 & 0 & 0 & 0
\end{bmatrix}
$$ 

(which is invertible, $$P^{-1} = P$$).

__Corollary 3:__  

$$
P = \begin{bmatrix}
A & B \\
0 & D
\end{bmatrix}
$$

has an inverse if and only if $$A, D$$ are invertible. Then, 

$$
P^{-1} = \begin{bmatrix}
A^{-1} & - A^{-1} B D^{-1}\\
0 & D^{-1}
\end{bmatrix}
$$

__Corollary 4:__ 

For $$B$$ invertible, 

$$
P = \begin{bmatrix}
A & B \\
0 & D
\end{bmatrix}
$$

has an inverse if and only if $$X = D B^{-1} A$$ is invertible. Then, 

$$
P^{-1} = \begin{bmatrix}
X^{-1} DB^{-1} & - X^{-1}\\
B^{-1} - B^{-1} A X^{-1} D B^{-1}  & B^{-1} A X^{-1}
\end{bmatrix}
$$

Very similar expressions also exist for lower triangular matrices. 


