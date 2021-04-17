---
layout: default
title:  "Eigenvalues, Eigenvectors and Jordan Forms"
date:   2021-04-13
categories: math
parent: Linear Algebra
math: katex
---

# Eigenvalues, Eigenvectors, Jordan Forms

There is more to eigenvalues than simply solving $$(A-\lamba I)v = 0$$.


## Eigenvales/vectors:

Lets assume we have a vector space $$V$$, over the field $$F$$. We are interested in studying linear operators $$\mathcal{A}$$ that map elements of the vector space $$V$$ to itself. That is 

$$
\mathcal{A} : V \rightarrow V
$$

Note, this isnt limited to matrix-vector operations: we can consider the vector space as being polynomials of degree 5 as the vector space, and let $$\mathcal{A}$$ be some linear operator like taking the derivative of a polynomial. We keep the above abstract and plough on. 

Suppose we have a $$\lambda \in F$$ and $$v \in V, v\neq 0$$ such that 

$$
\mathcal{A}(v) = \lambda v
$$

then, $$\lambda$$ is an **eigenvalue** and $$v$$ is an **eigenvector** of $$\mathcal{A}$$. 

A very important subspace of the vector space $$V$$ is the **eigenspace**:

$$ 
N_\lambda = \mathcal{N}(\mathcal{A} - \lambda \mathcal{I} )
$$

which is the null space of the operation $$\mathcal{A} - \lambda \mathcal{I}$$, that is, the subspace

$$
N_\lambda = \{ u\in V \text{ such that } \mathcal{A}(u) - \lambda u = 0 \}
$$

(notation: I use $$\mathcal{N}(f)$$ to denote the null space of an operator $$f$$, and $$N$$ to denote the eigenspace)

This allows us to define the **geometric multiplicity** $$q_i$$ of an eigenvector $$\lambda_i$$ as the *dimension of* $$N_{\lambda_i}$$. Notice, geometric, because the dimension defines the number of orthogonal directions which are in the eigenspace.

The **algebraic multiplicity** $$m_i$$ is the number of times that $$\lambda_i$$ is a repeated eigenvalue of $$A$$, ie is a solution to $$det(A - \lambda I) = 0$$. 


The rest of this article focuses on finite linear operators, ie, those which can be represented with a finite matrix $$A$$ using a given basis.

**Theorem:**
A matrix $$A \in F^{n\times n}$$ has $$n$$ solutions $$\lambda_i$$. *If all $$n$$ eigenvalues are distinct*, the associated eigenvectors $$v_i$$ form a basis $$Q$$ for the matrix $$A$$, and the matrix $$A$$ can be represented as 

$$
A Q =  Q \Lambda \\
\therefore A = Q \Lambda Q^{-1} \text{ and } \Lambda = Q^{-1} A Q
$$

where $$Q$$ collects the set of eigenvectors along the columns, and $$\Lambda$$ is a diagonal matrix of the associated eigenvales. Since the vectors form a basis, $$Q^{-1}$$ is always well defined.

## Jordan Forms:
But what if the eigenvalues are not distinct, that is, there are repeated eigenvalues? This is where the Jordan form is used. Note, the Jordan form is only a conceptual tool, and is not used in a computational setting. MATLAB provides a (very slow) `jordan` command, but the method is symbolic.

The Jordan form $$J$$ of a square matrix $$A$$ is composed of a set of Jordan blocks. Each Jordan block has the repeated eigenvalue $$\lambda_i$$ on the diagonals, and 




