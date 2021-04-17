---
layout: default
title:  "Jordan Form Example"
date:   2021-04-17
categories: math
parent: Linear Algebra
math: katex
---

# Jordan Form Example

A simple walked through example.

Consider the matrix

$$
A = \begin{bmatrix}0 & 4 & 3 \\ 0 & 20 & 16 \\ 0 & -25 & -20\end{bmatrix}
$$

and we wish to find the Jordan form $$A = V J V^{-1}$$ of this matrix.

## Step 1: Find Eigenvalues:
First we determine the eigenvalues:

$$
det(A - \lambda I) = 0
$$
and this has three repeated solutions $$\lambda = 0$$. 

Thus there is one unique eigenvalue $$\lambda_1 = 0$$, with algebraic multiplicity $$m_1 = 3$$.

## Step 2: Determine the form of the Jordan Block:

We use the geometric multiplicity, which is defined as the dimension of the eigen space:

$$
q_i = \text{dim} \mathcal{N}(A-\lambda I)
$$

In this case

$$
\mathcal N (A - 0 I ) = \text{span}\left(\begin{bmatrix}1 \\ 0 \\ 0\end{bmatrix}\right)
$$

and so $$q_1 = 1$$, and so there is only one type of Jordan block that we can have:

$$
J = \begin{bmatrix} 
0 & 1 & 0 \\
0 & 0 & 1 \\
0 & 0 & 0
\end{bmatrix} 
$$

normally, you will have a set of possible Jordan blocks, and which one it is depends on the index of the eigenvalue.

## Step 3: Find Generalized Eigenspaces:

In this case, we have a single eigenvector of rank 1, and so we will have a single chain of generalized eigenvectors. We start by finding the generalied eigenspaces:

$$
\mathcal{N}(A-\lambda I) = \mathcal{N}\begin{bmatrix}0 & 4 & 3 \\ 0 & 20 & 16 \\ 0 & -25 & -20\end{bmatrix} = \text{span} \left(\begin{bmatrix}1 \\ 0 \\ 0\end{bmatrix}\right)
$$

$$
\mathcal{N}\left((A-\lambda I)^2\right) = \mathcal{N}\begin{bmatrix}0 & -5 & 4 \\ 0 & 0 & 0 \\ 0 & 0 & 0\end{bmatrix} = \text{span} \left(\begin{bmatrix}1 \\ 0 \\ 0\end{bmatrix}, \begin{bmatrix}0 \\ 4 \\ 5\end{bmatrix}\right)
$$

$$
\mathcal{N}\left((A-\lambda I)^3\right) = \mathcal{N}\begin{bmatrix}0 & 0 & 0 \\ 0 & 0 & 0 \\ 0 & 0 & 0\end{bmatrix} = \text{span} \left(\begin{bmatrix}1 \\ 0 \\ 0\end{bmatrix}, \begin{bmatrix}0 \\ 1 \\ 0\end{bmatrix}, \begin{bmatrix}0 \\ 0 \\ 1\end{bmatrix}\right)
$$

## Step 4: Find the Generalised Eigenvectors:
and now we must work backwards - we must choose a $$v^3, v^2, v^1$$ such that 

$$
v^3  \in \mathcal{N}\left((A-\lambda I)^3\right) \text{ and } v^3 \not \in \mathcal{N}\left((A-\lambda I)^2\right)
$$

say we choose 

$$v^3 = \begin{bmatrix}0 \\ 0 \\ 1\end{bmatrix}$$

Then we can determine $$v^2$$:

$$
v^2 = (A - \lambda I) v^3 = \begin{bmatrix}3 \\ -16 \\ 20\end{bmatrix}
$$

and $$v^1$$:

$$
v^1 = (A - \lambda I) v^2 = \begin{bmatrix}4 \\ 0 \\ 0\end{bmatrix}
$$

which gives us the basis:

$$
V = \begin{bmatrix} v^1 & v^2 & v^3 \end{bmatrix} = 
\begin{bmatrix} 4 & 3 & 0\\ 0 & -16 & 0 \\ 0 & 20 & 1 \end{bmatrix}
$$

## Solution:
So the Jordan form is:

$$
J = \begin{bmatrix} 
0 & 1 & 0 \\
0 & 0 & 1 \\
0 & 0 & 0
\end{bmatrix} 
\quad 
V =\begin{bmatrix} 4 & 3 & 0\\ 0 & -16 & 0 \\ 0 & 20 & 1 \end{bmatrix}
$$

and you can verify that $$A = V J V^{-1}$$.

Note, a different choice for $$v^3$$ will give a different matrix $$V$$, but the Jordan form $$J$$ is the same, and the decomposition is also still valid.

Also important to note, you can't rescale generalised eigenvectors in quite the same way as with normal eigenvectors. If you rescale $$v^3$$ for instance, you have to recompute all the vectors in the chain so that they match up.