---
layout: default
title:  "Lyapunov Stability"
date:   2021-04-17
categories: math
parent: Nonlinear Systems
math: katex
---

# Lyapunov Stability

This tech note summarizes Chapter 4 of "Nonlinear Systems" by Hassan K. Khalil (3rd edition). I have tried to be as precise as possible, but one should refer to the textbook for rigourous definitions. The proofs are also not repeated here.

## Autonomous Systems:

Consider the system

$$
\dot x = f(x)
$$

where
- $$f: D \rightarrow R^n$$ is a locally Lipschitz map from $$D \sub R^n$$ to $$R^n$$
- $$x=0$$ is an equilibium point, ie $$f(0) =0$$

**Definitions**:
The equilibrium point $$x=0$$ is 
- **unstable** if not stable
- **stable** if, for every $$\epsilon > 0$$ there exists a $$\delta = \delta(\epsilon) > 0$$ such that 

$$
|| x(0) || < \delta \implies || x(t) || < \epsilon, \quad \forall t\geq 0
$$

- **asymptotically stable** if $$x=0$$ is stable, and $$\delta$$ can be chosen such that 

$$
||x(0) || < \delta  \implies \lim_{t\rightarrow 0} x(t) = 0
$$

-  **exponentially stable** if $$x=0$$ is stable, and constants $$L, \gamma$$ can be chosen such that 

$$
||x(0) || < \delta \implies ||x(t)|| < L \|x(0)\| e^{-\gamma t}, \quad \forall t\geq 0
$$



Note: locally Lipschitz does not imply that a solution is defined for $$t\geq0$$, but the additional conditions involved in Lyapunov stability guarantee this. 

----------------------------------------------------------------

**Theorem 4.1**:  Lyapunov's theorem

- Let $$x=0$$ be an equilibrium point
- Let $$D \sub R^n$$ be a domain containing $$x=0$$
- Let $$V : D \rightarrow R$$ be a continously differentiable function such that $$V$$ is positive definite. 

If $$\dot V$$ is negative semi-definite, *then $$x = 0$$ is stable*.

If $$\dot V$$ is negative definite, *then $$x=0$$ is asymptotically stable*.

----------------------------------------------------------------
Proof: Construct suitable subsets, and apply the definitions of stability etc.


### Some useful Lyapunov Functions
*Same sector nonlinearity*:

Suppose we have 

$$\dot x = - g(x)$$

where $$g(0) = 0$$ and $$x g(x) > 0$$ for all $$x \in D$$ and $$x \neq 0$$. Essentially looks like a feedback system. 

So we choose 

$$V(x) = \int_0^x g(y) dy$$

(which is positive definite) and now $$\dot V = g(x) \dot x = - g(x)^2$$ and so the Lyapunov conditions are satisfied.

*Variable Gradient Method:* 

For some $$V(x)$$ let 

$$
g(x) = (dV/dx)^T
$$

Then $$\dot V = g(x)^T f(x)$$ and we need the jacobian $$dg/dx$$ to be symmetric. So choose a $$g(x)$$ (based on what $$f$$ looks like) such that $$g(x)^T f(x)$$ is negative definite.

Then compute 

$$V(x) = \int_0^x g(y)^T dy = \int_0^{x_1} g_1(y_1, 0, ..., 0) dy_1 + \int_0^{x_2} g_2(0, y_2, 0, ..., 0) dy_2 + ... $$

If we chose $$g$$ with some unknown parameters, we can now choose those parameters such that the resulting $$V$$ is positive definite. (See example 4.5 of Khalil to see this in practice).

----------------------------------------------------------------
**Theorem 4.2**
- Let $$x=0$$ be an equilibrium point
- Let $$V : R^n \rightarrow R$$ be a continously differentiable function such that $$V$$ is positive definite on the entire domain.
- Let $$\|x\| \rightarrow \infty \implies V(x) \rightarrow \infty$$ (ie is radially unbounded)
- Let $$\dot V$$ be negative definite

*Then $$x=0$$ is globally asymptotically stable*

Globally just means that the property holds for all $$x \in R^n$$.

----------------------------------------------------------------
**Theorem 4.3:** Chetaev's theorem: (Converse Theorem)
- Let $$V: D \rightarrow R$$ be a continously differentiable function (not necessarily positive definite) and $$D$$ contains the origin, and $$V(0) = 0$$
- Suppose there exists a $$x_0$$ arbitrarily close to the origin such that $$V(x_0) > 0$$.
- Choose $$r> 0$$ such that $$B_r = \{x : \|x\| < r\}$$ is contained in $$D$$. 
- Let $$U = \{ x \in B_r : V(x) > 0\}$$
- Suppose that $$\dot V(x) > 0$$ for all $$x \in U$$

*Then $$x=0$$ is unstable*

----------------------------------------------------------------
Sketch of Proof: Notice, by construction, there exists $$x_0 \in U$$ where $$\dot V(x_0) > 0$$. Since $$x_0$$ can be chosen arbitrarily close to the origin, there exists an arbitrarily small ball (of radius $$r = \|x_0\|$$) such that the system must leave this ball. Thus, the origin cannot be stable.

**Definitions**
1. A set $$M$$ is said to be *invariant* with respect to $$\dot x = f(x)$$ if $$x(0) \in M \implies x(t) \in M \forall t$$. 
2. A set $$M$$ is *positively invariant* if (above) for all $$t\geq 0$$.
3. A system *approaches* a set $$M$$ if, for each $$\epsilon > 0$$ there exists a $$T = T(\epsilon) > 0$$ such that $$dist(x(t), M) < \epsilon$$ for all $$t \geq T$$.

----------------------------------------------------------------
**Theorem 4.4:** La Salle's Theorem

- Let $$\Omega \in D$$ be a compact set that is positively invariant with respect to $$\dot x = f(x)$$. 
- Let $$V : D \rightarrow R$$ be a continously differentiable function
- Let $$\dot V \leq 0$$ for all $$x \in \Omega$$ (no need for positive definite $$V$$)
- Let $$E = \{ x \in \Omega : \dot V = 0\}$$
- Let $$M$$ be the largest invariant set in $$E$$

*Then every solution starting in $$\Omega$$ approaches $$M$$ as $$t \rightarrow \infty$$.*

----------------------------------------------------------------
Note, if the only invariant point is $$M = \{ 0 \}$$ then the system must be asymptotically stable.

If you use these theorems to prove stability, you have used Lyapunov's direct method. The indirect method is based on linearization:

----------------------------------------------------------------
**Theorem 4.5:** 
The equilibrium point $$x=0$$ of $$\dot x = A x$$ is 
- *stable* if, and only if, 
  - eigenvalues of $$A$$ satisfy 
  
  $$Re(\lambda_i) \leq 0$$

  - AND for any eigenvalue with $$Re(\lambda_i) = 0$$ with algebraic multiplicity $$q_i \geq 2$$, we have 
  
  $$\text{dim} N(A-\lambda_i I) = q_i$$
  
  ie all the eigenvectors are of rank 1, (ie no generalised eigenvectors).
- *asymptotically stable*, if and only if 

$$Re(\lambda_i) < 0$$

 for all eigenvectors. That is, $$A$$ is a *Hurwitz* matrix.

----------------------------------------------------------------

**Theorem 4.6**

A matrix $$A$$ is Hurwitz, if, and only if, for any positive definite symmetric matrix $$Q$$ there exists a positive definite symmetric matrix $$P$$ such that 

$$
PA + A^T P = -Q
$$

Moreover, $$P$$ is unique.

Proof: To prove sufficiency, let $$V(x) = x^T P x$$ work through $$\dot V$$, and define $$Q$$ as needed. To prove necessity, define $$P = \int_0 ^\infty \exp(A^T t) Q \exp(At) dt$$, which must exist since $$A$$ is stable. And then prove that $$P$$ is positive definite. Substitute $$P$$ into the Lyapunov equation, and show that it can be simplified to $$-Q$$.

Note $$Q$$ can be relaxed to positive semi-definite, if $$Q = C^T C$$ and $$(A, C)$$ is observable.

----------------------------------------------------------------

The key benefit of this method is that we can make the following claim:

**Theorem 4.7** 
- Let $$x=0$$ be an equilibrium point for the nonlinear system $$\dot x = f(x)$$. 
- Let 

$$
A = \frac{df}{dx}(x) |_{x=0}
$$

If $$A$$ is Hurwitz, *then the nonlinear systme is asymptotically stable.*

If $$Re(\lambda_i) \geq 0$$ for one or more eigenvalues, *then the origin is unstable*.

Proof: essentially you can show that there is a small enough neighborhood of the origin that is dominated by the linear term $$\dot x = Ax$$.

Note, if $$Re(\lambda_i) = 0$$ for some $$\lambda_i$$, the system could be stable, asymptotically stable, or unstable! - there is no way to know, without greater analysis 

----------------------------------------------------------------


## Non-Autonomous Systems

Now we extend these ideas to systems of

$$
\dot x = f(t, x)
$$

where
- $$f : [0, \infty) \times D \rightarrow R$$
- $$f(t, 0) = 0$$ for all $$t \geq 0$$. (this can be assumed, since we can change coordinates: $$x = y - \bar y$$, where $$\bar y$$ is a solution to the above equation) 
- $$D$$ contains $$x=0$$.
- $$f$$ is piecewise continous in $$t$$
- $$f$$ is locally Lipschitz in $$x$$ on $$[0, \infty) \times D$$. 

**Definitions:**
The equilibrium point $$x=0$$ for the non-autonomous system is 
- **unstable** if it is not stable.
- **stable**, if, for every $$\epsilon > 0$$ there exists a $$\delta = \delta(\epsilon, t_0) > 0$$ such that 

$$ \|x(t_0)\| < \delta(\epsilon, t_0)  \implies \| x(t) \| < \epsilon, \quad \forall t \geq t_0 \geq 0$$ 

-  **uniformly stable** if for each $$\epsilon > 0$$, there is a $$\delta = \delta(\epsilon) >0$$,  independent of $$t_0$$, such that 

$$ \|x(t_0)\| < \delta(\epsilon) \implies \| x(t) \| < \epsilon, \quad \forall t \geq t_0 \geq 0$$ 

-  **asymptotically stable** if it is stable, and there is a positive constant $$c = c(t_0) > 0$$ such that 

$$
\lim_{t\rightarrow\infty} x(t)  = 0, \quad \forall \| x(t_0) \| < c(t_0)
$$

-  **uniformly asymptotically stable** if it is uniformly stable, and there is a $$c >0$$, independent of $$t_0$$, such for all $$\|x(t_0) \| < c$$,

$$
x(t) \rightarrow 0 \text{ as } t \rightarrow \infty
$$

That is, for each $$\eta > 0$$, there is a $$T = T(\eta) > 0$$ such that 

$$
\| x(t) \| < \eta, \quad \forall t \geq t_0 + T(\eta), \quad \forall \|x(t_0)\| < c 
$$

-  **globally uniformly assymptotically stable** if 
  1. it is uniformly stable,
  2. $$\delta(\epsilon)$$ can be chosen to statisfy $$\lim_{\epsilon \rightarrow \infty} \delta(\epsilon) = \infty$$
  3. for each pair of positive numbers $$\eta, c > 0$$ there exists a $$T(\eta, c) > 0$$ such that

    $$ \| x(t) \| < \eta, \quad \forall t\geq t_0 + T(\eta, c), \quad \forall \|x(t_0)\| < c$$



----------------------------------------------------------------
**Lemma 4.5**
The equilibrium point $$x=0$$ is 
- **uniformly stable** if, and only if, there exists a class $$K$$ function $$\alpha$$ and a positive constant $$c>0$$ independent of $$t_0$$ such that 

$$
\| x(t) \| < \alpha(\| x(t_0) \|), \quad \forall t \geq t_0 \geq 0, \quad \forall \| x(t_0) \| < c
$$

- **uniformly asymptotically stable** if, and only if, there exists a class $$KL$$ function $$\beta$$ and a positive constant $$c>0$$ independent of $$t_0$$, such that

$$
\| x(t) \| < \beta ( \| x(t_0)\| , t- t_0),  \quad \forall t \geq t_0 \geq 0, \quad \forall \| x(t_0) \| < c
$$

- **exponentially stable** if there exist positive constants $$c, k, \lambda > 0$$ such that 

$$
\| x(t) \| < k  \| x(t_0)\| e^{-\lambda (t-t_0)},  \quad \forall \| x(t_0) \| < c
$$

- **globally exponentially stable** if the system is exponentially stable for all $$x(t_0)$$.

----------------------------------------------------------------

----------------------------------------------------------------

**Theorem 4.8** Lyapunov's theorem for non-autonomous systems:
- Let $$x=0$$ be an equilibrium point
- Let $$D \sub R^n$$ be a domain containing $$x=0$$.
- Let $$W_1(x), W_2(x)$$ be continous positive definite functions on $$D$$.
- Let $$V : D \rightarrow R$$ be a continously differentiable function such that 

$$ W_1(x) \leq V(t, x) \leq W_2(x)$$

$$ \frac{\partial V}{\partial t} + \frac{\partial V}{\partial x} f(t, x) \leq 0$$ 

for all $$t \geq 0$$ and for all $$x \in D$$. 

*Then $$x=0$$ is uniformly stable*.

**Theorem 4.9** extends this: if we also have

$$ \frac{\partial V}{\partial t} + \frac{\partial V}{\partial x} f(t, x) \leq -W_3(x)$$

where $$W_3$$ is a positive definite function on $$D$$, *then $$x=0$$ is uniformly asymptotically stable.*

If $$D = R^n$$ and $$W_1$$ is radially unbouded, *then $$x=0$$ is globally uniformly asymptotically stable*.

**Theorem 4.10** says if there exists $$k_1, k_2, k_3, a > 0$$ such that it is uniformly asymptotically stable with $$W_1(x) = k_1 \|x\| ^ a, W_2(x) = k_2\|x\|^a, W_3(x) = k_3\|x\| ^a$$, *then the origin is exponentially stable*. 

If this holds globally, *it is globally exponentially stable*.

## todo

1. application of linearization to non-autonomous systems, and the Lyapunov differential equations
2. converse theorems (these essentially say that stability of some form exist, a Lyapunov function, satisifying the corresponding conditions exists)
3. boundedness and ultimate boundedness (which is a relaxation on stability requirements)
4. Input to state stability (which leads to $$\mathcal{L}$$ stability, as in Chapter 5)