---
layout: default
title:  "Closed Form QP"
date:   2021-07-08
categories: math
parent: Interesting Maths
math: katex
---

# Closed Form Solutions to Quadratic Programs

In general, the solution to quadratic programs (QP) can be found very efficiently by using open source solvers, for instance [OSQP](https://osqp.org/). However, in some specific instances, a closed form solution can also be found, and it might be useful for implementation or analysis. Here I present a few such solutions.

## QP with a single constraint, V1

A general QP of the above form is 

$$
\begin{array}{rl}
\text{minimize} & \frac{1}{2} x^T P x + q^T x\\
\text{subject to} & a^T x \leq b
\end{array}
$$

where 
- $$x \in R^n$$ is the optimization variable
- $$P \in S^{n \times n}$$ is a symmetric positive definite matrix (note, OSQP allows positive semi-definite matrices too, but below I assume the matrix is invertible), and represents the quadratic cost term
- $$q \in R^n$$ is the linear cost term
- $$a \in R^n$$ is the linear constraint vector, assumed non-zero
- $$b \in R$$  is the linear constraint offset
- Also let $$\mu \in R$$, $$\mu > 0$$ be the dual variable for the constraint

Since this is a convex optimization problem with a single constraint, it is always feasible. Thus, by Slater's condition, the necessary and sufficient conditions for a solution $$x, \mu$$ to be the optimal solution are the KKT conditions. For the above problem, we have: 

- Stationarity:

$$
x^T P + q^T + \mu a^T = 0
$$
 
- Primal Feasibility:

$$
a^T x \leq b
$$

- Dual Feasibility:

$$
\mu \geq 0
$$

- Complementary Slackness:

$$\mu (a^T x - b) = 0$$


Which means that there are two possible cases:

**Case 1: The constraint is inactive**

Thus $$\mu = 0$$ and $$x^T P = q^T$$ which implies

$$
x^* = - P^{-1} q
$$

**Case 2: The constraint is active**

Thus $$a^T x = $$ and we must solve for a suitable value for $$x, \mu$$. We have two equations, which can be arranged into a block matrix:

$$
\begin{bmatrix} 
P & a\\
a^T & 0
\end{bmatrix}
\begin{bmatrix} 
x \\
\mu
\end{bmatrix}
=
\begin{bmatrix} 
- q\\
b
\end{bmatrix}
$$

and thus the solutions is

$$
\begin{bmatrix} 
x^* \\
\mu^*
\end{bmatrix}
=
\begin{bmatrix} 
P & a\\
a^T & 0
\end{bmatrix}^{-1}
\begin{bmatrix} 
- q\\
b
\end{bmatrix}
$$

Using [block matrix inversion](https://en.wikipedia.org/wiki/Block_matrix#Block_matrix_inversion), we can compute the inverse explicitly,

$$
\begin{bmatrix} 
x^* \\
\mu^*
\end{bmatrix} = 
\begin{bmatrix} 
P^{-1} + P^{-1} a S^{-1} a^T P^{-1} & -P^{-1} a S^{-1}\\
- S^{-1}  a^T P^{-1} & S^{-1}
\end{bmatrix}
\begin{bmatrix} 
- q\\
b
\end{bmatrix}
$$

where $$S = - a^T P^{-1} a$$ is the schur complement, which in this case is a scalar value. This allows the expressions to be simplified:

$$
x^* = - P^{-1} q + \frac{a^T P^{-1} q + b}{a^T P^{-1} a} P^{-1} a
$$

$$
\mu^* = -\frac{ (a^T P^{-1} q  + b)}{a^T P^{-1} a}
$$


## QP with single constraint, V2:

The expressions are a bit simpler, if we rewrite the optimization problem as 

$$
\begin{array}{rl}
\text{minimize} & \frac{1}{2} (x-x_0)^T P (x-x_0)\\
\text{subject to} & a^T x \leq b
\end{array}
$$

where $$x_0$$ is the optimal solution without any constraints. Again, we have two cases:

**Case 1: Constraint is not active:**

$$
x^* = x_0
$$

$$
\mu^* = 0
$$

**Case 2: Constraint is active:**

Running through the same steps as above (or by realising $$x_0 = -P^{-1}q$$ for the above notation), we arrive at

$$
x^* = x_0 - \frac{a^T x_0 - b}{a^T P^{-1} a} P^{-1} a
$$

$$
\mu^* = \frac{ a^T x_0  - b}{a^T P^{-1} a}
$$

**Both Cases**

Both cases can be summarized into a single expression as:

$$
x^* = x_0 - \frac{\text{max}\{0, a^T x_0 - b\}}{a^T P^{-1} a} P^{-1} a
$$


## CBF QP

For my own reference, I am writing the solution in terms of the variables we tend to use in my research:

$$
\begin{array}{rl}
\text{minimize} & \frac{1}{2} (u-u_0)^T P (u-u_0)\\
\text{subject to} & L_fh + L_gh u \geq -\alpha h
\end{array}
$$


If $$L_fh + L_gh u_0 + \alpha h  \geq 0$$: 

$$
u^* = u_0
$$

Else:

$$
u^* = u_0 - \frac{L_fh + L_gh u_0 + \alpha h}{L_gh P^{-1} L_gh^T} P^{-1} L_gh
$$


## CLF QP

$$
\begin{array}{rl}
\text{minimize} & \frac{1}{2} (u-u_0)^T P (u-u_0)\\
\text{subject to} & L_fV + L_gV u \leq -\alpha V
\end{array}
$$

If $$L_fV + L_gV u_0 + \alpha h  \leq 0$$: 

$$
u^* = u_0
$$

Else:

$$
u^* = u_0 - \frac{L_fV + L_gV u_0 + \alpha V}{L_gV P^{-1} L_gV^T} P^{-1} L_gV
$$

Note these expressions break when $$L_gh=0$$ or $$L_gV=0$$. Higher order CBFs are required in this case. 


## CLF-CBF-QP

Refer to [this paper](https://authors.library.caltech.edu/79450/2/1609.06408.pdf) for the explicit expressions, but at this point, its probably better to just use OSQP or Convex.jl.