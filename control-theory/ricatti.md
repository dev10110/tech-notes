---
layout: default
title:  "Algebraic Ricatti Equations"
date:   2025-08-05
categories: controls theory math
parent: Notes on Control Theory
math: katex
---


# Solving the Algebraic Ricatti Equation


The Ricatti equations arise all over the place in control theory. While there are mature packages like [MatrixEquations.jl](https://github.com/andreasvarga/MatrixEquations.jl/tree/master) to solve them, it is interesting to see how they are actually solved. This document summarizes how and why these are solved. 

Let $$\mathbb{P}(n)$$ denote the set of symmetric positive definite matrices in $$\mathbb{R}^{n \times n}$$. 

The goal is to solve equations of the following forms

**Continuous-time Algebraic Ricatti Equation (CARE)**

$$
A^T X + X A - X B R^{-1} B X + Q = 0
$$

where $$A \in \mathbb{R}^{n \times n}$$, $$B \in \mathbb{R}^{n \times p}$$, $$R \in \mathbb{P}(p)$$ and $$Q \in \mathbb{P}(n)$$. The goal is to find $$X \in \mathbb{P}(n)$$. 

**Discrete-time Algebraic Ricatti Equation (DARE)**

$$
X = A^T X A - A^T X C( R + C^T X C)^{-1} C^T X A + Q 
$$

where $$A \in \mathbb{R}^{n \times n}$$, $$C \in \mathbb{R}^{p \times n}$$, $$R \in \mathbb{P}(p)$$, and $$Q \in \mathbb{P}(n)$$. The goal is to find $$X \in \mathbb{P}(n)$$. 

These equations arise naturally in LQG problems, as well as in many Kalman Filtering problems. 

## Relevant References:
- Pappas, Thrasyvoulos, Alan Laub, and Nils Sandell. "On the numerical solution of the discrete-time algebraic Riccati equation." IEEE Transactions on Automatic Control 25.4 (1980): 631-641.
- Laub, Alan. "A Schur method for solving algebraic Riccati equations." IEEE Transactions on automatic control 24.6 (2003): 913-921.
- Arnold, William F., and Alan J. Laub. "Generalized eigenproblem algorithms and software for algebraic Riccati equations." Proceedings of the IEEE 72.12 (2005): 1746-1754.

I really like the first paper. The third paper provides various generalizations of the algorithm, and discusses numerical implementations. Here is a particularly fun quote from the third paper:

> The authors chose to implement the algorithms as high-
quality mathematical software because to do less would be
to evade a major responsibility, for to leave software imple-
mentation to the algorithm user/reader has the potential to
lead to disastrously bad "versions" of a perfectly good
algorithm.


## Algorithms

I often find it useful to see a clean algorithm, and here it is particularly useful.While I strongly recommend using `arec` or `ared` from [MatrixEquations.jl](https://github.com/andreasvarga/MatrixEquations.jl/tree/master), here is a minimal algorithm representing how to solve the CARE/DARE problems.


```julia
# works on julia v1.11

using LinearAlgebra

function simple_arec(A, G, Q)
    # solves A'X + XA - XGX + Q = 0

    # construct the hamiltonian
    Z = [
            [ A ;; -G ];
            [ -Q ;; -A' ]
        ]

    # do a schur decomposition on H
    # F = schur(A) <==> A = F.vectors * F.Schur * F.vectors'
    F = schur(Z)

    # now need to reorder the columns such that the real parts of spectrum of Λ_11 are negative
    
    # get the eigenvalues
    λ = F.values

    # determine the reordering
    select = λ .< 0

    # reorder
    ordschur!(F, select)

    # extract the schur decomposition 
    U = F.vectors
    Λ = F.Schur

    # Λ has the structure 
    # Λ = [  S11 ;; S12 ]
    #     [    0 ;; S22 ]

    # U has the structure
    # U = [  U11 ;; U12 ]
    #     [  U22 ;; U22 ]

    # extract the solution
    U11 = U[1:n, 1:n]
    U21 = U[(n+1):(2n), 1:n]
    X = U21 * inv(U11)
    
    # return the symmetric version for numerical reasons
    return (X + X')/2
    
end


function simple_ared(A, B, R, Q)
    # solves A'XA - X - (A'XB)(R+B'XB)^(-1)(B'XA) + Q = 0

    # get the dimension
    n = size(A, 1)

    # extract a useful matrix
    G = B * inv(R) * B'

    # construct the symplectic matrix
    AinvT = inv(A)'
    Z = [
            [ A + G * AinvT * Q;;  -G * AinvT];
            [ -AinvT * Q       ;;  AinvT     ]
        ]

    # do the schur decomposition
    F = schur(Z)

    # get the eigenvalues of F
    λ = F.values

    # reorder the decomposition (abs is in a complex sense)
    # this ensures all the eigenvectors corresponding to stable solutions are in the first n columns
    select = abs.(λ) .< 1 
    ordschur!(F, select)
    
    # extract the subspaces
    U = F.vectors
    Λ = F.Schur

    # extract the solution
    U11 = U[1:n, 1:n]
    U21 = U[(n+1):(2n), 1:n]

    # construct the solution
    X = U21 * inv(U11)

    # return the symmetric version of it (for numerical reasons)
    return (X + X')/2
    
end
```

This should work, but does not use any advanced techniques like balancing that can help improve the numerical accuracy. 

## Why it works (CARE)

Let us now explain why and how this works. Of course, it depends on things like $$(A, C)$$ being detectable, but I will not bother laying out these here. See the papers. 

Recall the goal is to solve 

$$
A^T X + X A - X B R^{-1} B X + Q = 0
$$

for $$X \in \mathbb{P}(n)$$. 

The core idea is to use the connection between these problems and optimal control theory to find stable solutions. Consider a continuous time LQR problem:

$$
\begin{align}
\min_{u(t)} \ & \int_{0}^\infty \frac{1}{2} ( x^T Q x + u^T R u) dt \\
\text{s.t.} \ & \dot x = A x + B u(t)
\end{align}
$$

From optimal control theory, we can now write down a Hamiltonian for this problem:

$$
H = \frac{1}{2} x^T Q x + \frac{1}{2} u^T R u + y^T (A x + B u)
$$

where $$y \in \mathbb{R}^n$$ is the co-state.  The optimality conditions are 

$$
\begin{align}
\dot x &= dH/dy\\
\dot y &= - dH/dx\\
dH/du &= 0
\end{align}
$$

The last equation means that $$u$$ is given by 

$$
u^T R + y^T B u = 0
$$

so

$$
u = - R^{-1} B^T y
$$

So $$H$$ can be expressed as 

$$
H = \frac{1}{2} x^T Q x - \frac{1}{2} y^T B R^{-1} B^T y + y^T A x 
$$ 

and dynamics of $$x, y$$ are given by

$$
\begin{align}
\dot x &= - B R^{-1} B^T y + A x\\
\dot y &= - Q x - A^T y
\end{align}
$$

This can be expressed as the matrix equation

$$
\frac{d}{dt}
\begin{bmatrix}
x \\ y
\end{bmatrix}
=
\begin{bmatrix}
A & - B R^{-1} B^T \\
- Q &  - A^T
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix}
$$

and now the goal is relatively straightforward: we want to find a stable solution of this problem, since it will represent the finite-horizon solution, which corresponds to the CARE solution. 

So now we can define a matrix 

$$
Z = 
\begin{bmatrix}
A & - B R^{-1} B^T \\
-Q & -A^T
\end{bmatrix}
$$

and the goal is to find solutions $$s = [x, y]^T$$ such that $$Zs = 0$$. 


To do this, we shall use the Schur decomposition of $$Z$$. The schur decomposition of $$Z$$ gives us two new matrices, $$U \in \mathbb{R}^{2n \times 2n}$$ and $$\Lambda \in \mathbb{R}^{2n \times 2n}$$ such that 

$$
Z = U \Lambda U^{-1}
$$

where 
- $$U$$ is a unitary matrix, i.e., $$U^{-1} = U^T$$,
- $$\Lambda$$ is an upper triangular matrix. 

If we do this, we will notice that some of the eigenvalues of $$\Lambda$$ (equivalently of $$Z$$) will have negative real components, and some will have positive real components. In particular, there will be exactly $$n$$ positive eigenvalues, and $$n$$ negative eigenvalues. 


We want to find the subspace of $$s = [x, y]^T$$ such that the solutions of $$\dot s = Z s$$  are stable. These will correspond to the negative real eigenvalues.  Hence we re-order the columns of $$U, \Lambda$$ to ensure that the negative real-eigenvalues are in the first $$n$$ columns. In `julia` this is done using the function `ordschur!`. To extract the solution, we now partion $$U$$ as follows:

$$
U = \begin{bmatrix}
U_{11} & U_{12} \\
U_{21} & U_{22}
\end{bmatrix}
$$

<!-- 
TODO(dev): add more details on why the solution is as it is

Notice that the first $$n$$ columns define a vector-space for $$[x, y]^T$$ such that the solutions are stable. Recall the value function for the optimal control problem is 

$$
\begin{align}
V(x_0) &= \frac{1}{2} \int_{0}^{\infty} x^T Q x + u^T R u dt\\
&= \frac{1}{2}\int_{0}^{\infty} x^T Q x + y^T B R^{-1} B^T y dt\\
&= \frac{1}{2} \int_0^\infty \begin{bmatrix}x^T && y^T\end{bmatrix}
\begin{bmatrix}
Q & 0 \\
0 & B R^{-1} B 
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix} dt\\
&= \frac{1}{2} \int_0^\infty e^{Z t} s_0 dt
\end{align}
$$

-->
Then the solution is  (todo: explain why this is true more clearly)

$$
X = U_{21} U_{11}^{-1}
$$

And we are done!


A very similar idea holds for the DARE problem. 



































































