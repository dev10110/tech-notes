---
layout: default
title:  "Grammians"
date:   2021-04-13
categories: controls theory math
parent: Notes on Control Theory
math: katex
---

# Understanding Reachability, Controllability and Observability Grammians

I've had trouble understanding how grammians worked, and how to use them, until I came across this basically one line proof by Emilio Frazzoli. I thought I'd repeat it here, so I'd never lose it. 

Reference: [Emilio Frazzoli Lecture 20 Reachability and Controllability](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-241j-dynamic-systems-and-control-spring-2011/lecture-notes/MIT6_241JS11_lec20.pdf)

## Reachability/Controllability Grammians

### System: 
Consider the $$n$$-dimensional Linear Time Varying (LTV) system

$$ 
\begin{equation}
\dot x = A(t) x + B(t) u 
\end{equation}
$$

with initial condition $$x(t_0) = 0$$. 

Note $$x \in \mathbb{R}^n$$, $$u \in \mathbb{R}^m$$. 


### Definitions: 
We say that a state $$x_1 \in \mathbb{R}^n$$ is **reachable** in time $$t_1 \geq t_0$$, if there exists a control input $$u : [t_0, t_1] \rightarrow \mathbb{R}^m$$ such that $$x(t_1) = x_1$$. 

To determine the reachable subsets, for Linear Time Invariant (LTI) systems, the standard controllability rank test can be performed. This is not the case for LTV systems, and motivates the definition reachability grammian defined below.


Any solution to the system (1) will have the following form:

$$ 
\begin{equation}
x(t_1) = \int_{t_0}^{t_1} \phi(t_1, \tau) B(\tau) u(\tau) d\tau
\end{equation}
$$

We can write this more succinctly as 

$$ 
\begin{equation}
x(t_1) = \int_{t_0}^{t_1} F^T(\tau) u(\tau) d\tau
\end{equation}
$$

which you might notice is the inner product between two functions:

$$ 
\begin{equation}
x(t_1) = \prec F , u \succ
\end{equation}
$$

and now the **reachability grammian** is simply defined as

$$
\begin{equation}
W_R (t_0, t_1) \triangleq \prec F, F \succ = \int_{t_0}^{t_1} \phi(t_1, \tau) B(\tau) B(\tau)^T \phi(t_1, \tau)^T d\tau
\end{equation}
$$

By this construction, $$W_R$$ is a symmetric, positive-definite real matrix of dimension $$n \times n$$. 

The magical property of the reachability grammian is:

### Main Theorem:
Let 
- $$\mathcal{R}$$ be the set of reachable states for system (1), and 
- $$W_R = W_R(t_0, t_1)$$ be the reachability grammian defined above. 

Then,

$$
\begin{equation}
 \boxed{ x_1 \in \mathcal{R} \iff x_1 \in Ra(W_R)}
\end{equation}
$$
 
*In words, every reachable state is in the range of the reachability grammian, **and** every state in the range of the reachability grammian is reachable.*

This allows us to study reachability (which involves considering every function $$u(t)$$) by simply studying a $$n \times n$$ real matrix! 

**Proof:**

We need to show two things:

1. $$ x_1 \in Ra(W_R) \implies x_1 \in \mathcal{R}$$
2. $$ x_1 \in \mathcal{R} \implies x_1 \in Ra(W_R)$$


*Part 1*::  Show $$ x_1 \in Ra(W_R) \implies x_1 \in \mathcal{R}$$:

Since $$x_1 \in Ra(W_R)$$ we know that there must exist a vector $$\eta \in \mathbb{R}^n$$ such that 

$$
\begin{align}
x_1&= W_r \eta\\
&= \left[ \int_{t_0}^{t_1} F^T(\tau) F(\tau) d\tau \right] \eta\\
&= \int_{t_0}^{t_1} F^T(\tau) \underbrace{F(\tau) \eta}_{u(\tau)} d\tau\\
&= \int_{t_0}^{t_1} F^T(\tau) u(\tau) d\tau\\
\end{align}
$$

comparing this to the solution of the system (3),  *if we choose*

$$
\begin{equation}
u(t) = F(t) \eta = B(\tau)^T \phi(t_1, \tau)^T \eta
\end{equation}
$$

then 

$$
\begin{equation}
x(t_1) = \int_{t_0}^{t_1} F^T(\tau) u(\tau) d\tau = W_R \eta = x_1
\end{equation}
$$

Therefore, we have found a control input $$u(t)$$ such that $$x_1$$ is reachable in time $$t_1$$. 


*Part 2*:: Show $$ x_1 \in \mathcal{R} \implies x_1 \in Ra(W_R)$$:

This is equivalent to showing:

$$ \mathcal{R}  \subseteq Ra(W_R)$$

which is equivalent to showing:

$$ \mathcal{R}^\perp \supseteq Ra(W_R)^\perp $$

where $$S^\perp$$ is the orthogonal complement of the subspace $$S$$.

Thus, we need to show that every $$x_u \in Ra(W_R)^\perp$$  also satisfies $$ x_u \in \mathcal{R}^\perp$$.

$$
\begin{align}
x_u \in Ra(W_R)^\perp &\implies x_u \in Null(W_R)\\
 &\implies W_R x_u = 0\\
&\implies x_u^T W_R x_u = 0\\
&\iff \prec F x_u, F x_u \succ = 0\\
&\iff F(t) x_u = 0, \quad \forall t\in [t_0, t_1]\\
&\iff x_u^T F(t)^T= 0, \quad \forall t\in [t_0, t_1]\\
&\implies x_u^T F(t)^T u(t)= 0, \quad \forall t\in [t_0, t_1] \quad [\text{ for any } u(t)]\\
&\implies x_u^T \underbrace{\int_{t_0}^{t_1} F(\tau)^T u(\tau) d\tau}_{x(t_1)} = 0 \quad [\text{ for any } u(\cdot)]\\
&\implies x_u^T x(t_1) = 0
\end{align}
$$

where $$x(t_1)$$ represents any reachable state. 

Thus, we see that $$x(t_1)$$ is orthogonal to $$x_u$$, and so we have established that $$x_u \in Ra(W_R)^\perp \implies x_u \in \mathcal{R}^\perp$$. 

Therefore, we have shown that $$x_1 \in \mathcal{R} \implies x_1 \in Ra(W_R)$$. $$\quad \square$$

Note, this proof does not assume any form for $$u(t)$$, as some few proofs I have come across seem to do.


### Application to designing a controller:

The question is now to find a controller that can transfer the sysyem from state $$x_0 = x(t_0)$$ to some state $$x_1 = x(t_1)$$, and we assume both of these may or may not be 0.

Therefore, we need to find a $$u(t)$$ such that 

$$
\begin{equation}
x_1 = \phi(t_1, t_0) x_0 + \int_{t_0}^{t_1} \phi(t_1, \tau) B(\tau) u(\tau) d\tau
\end{equation}
$$

Then, by the above definitions, we must have the vector/state

$$
\delta \triangleq  x_1 - \phi(t_1, t_0) x_0
$$

be reachable. Suppose it is. Then, there exists a $$\eta$$ such that 

$$
\delta = W_R \eta \implies \eta = W_R^{-1} \delta
$$

and we can find it. Then, if we apply the controller

$$
u(t) = F(t)^T \eta = B^T(t) \phi(t_1, t)^T  W_R^{-1} \delta
$$

(which is an open-loop controller), the state will transition from $$x_0$$ to $$x_1$$ over the interval $$[t_0, t_1]$$. 



Note: Computing the reachability grammian for a LTV system, is, in general, not simple. It involves computing $$n^2$$ integrals over the specified time domain. If the system is a LTI system, the Lyapunov equation can be solved to determine the reachability grammian.  


## Motivation for $$W_r = \prec F, F \succ$$

We know from the solution of linear systems that 

$$
x(t_1) = \int_{t_0}^{t_1} \phi(t_1, \tau) B(\tau) u(\tau) d\tau
$$

if $$x(t_0) = 0$$. 

This operation is actual a linear operation:

$$
x_1 = \mathcal{L}_u( u(\cdot))
$$

where $$\mathcal{L}_u$$ is a linear operator that takes in a function $$u : [t_0, t_1] \rightarrow R^m$$, and returns a state $$x_1 \in R^n$$. Thus we can write:

$$
\mathcal{L}_u : C([t_0, t_1], R^m) \rightarrow R^n
$$

And now the set of reachable states must be the range of this linear operator: $$\mathcal{R} = R(\mathcal{L}_u)$$. Since $$\mathcal{L}_u$$ isn't a matrix, you can't directly use Matlab or Julia to compute $$R(\mathcal{L}_u)$$. Nonetheless, we plough on.

We know from linear algebra that for any linear operator $$\mathcal{A}$$, 

$$
R(\mathcal{A}) = R(\mathcal{A} \mathcal{A}^*)
$$

that is the range of $$\mathcal{A}$$ is the same as the range of $$\mathcal{A}\mathcal{A}^*$$. 

Applied to $$\mathcal{L_u}$$, we have 

$$
R(\mathcal{L_u}) = R(\mathcal{L}_u \mathcal{L}_u^*)
$$

And now notice that 

$$
\mathcal{L}_u \mathcal{L}_u^* : R^n \rightarrow R^n
$$

which means we might be able to represent it as some matrix. We need to find $$\mathcal{L}_u^*$$ first:

Using the properties of the adjoint,

$$
\begin{align*}
\prec \mathcal{L}_u(u(\cdot)), x_1 \succ &=(\mathcal{L}_u ( u(\cdot)))^T x_1\\
&= \left( \int_{t_0}^{t_1} u(\tau)^T B(\tau)^T \phi(t_1, \tau)^T d\tau \right) x_1\\
&= \prec u(\cdot), B(\cdot)^T \phi(t_1, \cdot)^T x_1\succ\\
&= \prec  u(\cdot), \mathcal{L}_u^* (x_1) \succ
\end{align*}
$$

and thus 

$$
\mathcal{L}_u^* (x_1) = B(\cdot)^T \phi(t_1, \cdot)^T x_1
$$

so 

$$
\mathcal{L}_u \mathcal{L}_u^* = \int_{t_0}^{t_1} \phi(t_1, \tau) B(\tau) B(\tau)^T \phi(t_1, \tau)^T d\tau
$$

which is the reachability grammian. 

The above analysis is pretty much the same as that above, where $$F = \mathcal{L}_u$$. The proof is slightly different, and shows how the properties of linear algebra can be applied far beyond just matrices.
