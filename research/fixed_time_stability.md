---
layout: default
title:  "Fixed Time Stability"
date:   2021-05-18
categories: math
parent: Research
math: katex
---

# Fixed and Finite Time Stability

These notes summarise the results from Kunal Garg's papers on Fixed and Finite Time Stability. The notation is changed slightly, to a form that I prefer. Any mistakes in these notes are almost certainly mine. Please let me know if you spot anything weird. 

The key references are:
- [Ref 1] "Characterization of Domain of Fixed-time Stability under Control Input Constraints", Kunal Garg and Dimitra Panagou
- [Ref 2] Kunal Garg's PhD Thesis

---------------------------------------

## Definitions

These definitions are from [Ref 2].

Consider the autonous system 

$$
\dot x = f(x)
$$

with state $$x \in R^n$$, with continuous function $$f : R^n \rightarrow R^n$$ with $$f(0) = 0$$. We will assume that the solution exists, and is unique.

The equilibrium point $$ x= 0$$ is **finite-time stable (FTS)**,  if  the equlibirum point is stable, and there exists and there exists an open neighborhood $$N$$ of the origin, such that for all $$x(0) \in N \backslash \{0\}$$ there exists a $$T(x(0)) \in (0, \infty)$$ such that 

$$
\lim_{t\rightarrow T(x(0))} x(t) = 0
$$

The equilibrium point is **fixed time stable (FxTS)** from a domain $$D$$, if the equilibrium point is stable and if $$T \in (0, \infty)$$ can be chosen independently of $$x(0)$$ such that for all $$x(0) \in D$$ 

$$
\lim_{t\rightarrow T} x(t) = 0
$$

---------------------------------------
## Theorem 1 [FTS]

Taken from [Ref 2].

<div class="code-example" markdown="1">

Suppose there exists a positive definite, continuously differentiable function $$V : D \rightarrow R$$, $$\alpha >0$$, $$\mu > 1$$ such that 

$$
\dot V(x) \leq -\alpha V(x)^{1-1/\mu}, \quad \forall x \in N \backslash \{0\}
$$

where $$N \subset D$$ is an open neighborhood of the origin. 

Then the origin is *finite time stable* from the domain $$D$$, with a settling time 

$$
T(x_0) \leq \frac{\mu}{\alpha} V_0^{1/\mu}
$$

where $$V_0 = V(x(0))$$.

</div>

Notice that in the limit that $$V_0 \rightarrow \infty$$, the settling time also approaches infinity, i.e., $$T(x_0) \rightarrow \infty$$. Therefore, no fixed-time stability conditions can be provided.

**Rough Proof**:

We can use a comparison system $$h(t)$$, where $$h(0) = h_0 = V_0$$. Then we impose 

$$
\dot h = -\alpha h^{1-1/\mu}
$$

which implies that $$h(t) \geq V(x(t))$$ for all $$t \geq 0$$. Integrating both sides of the equation we get

$$
\begin{align*}
\int_{h_0}^{h_f} \frac{1}{ h^{1-1/\mu}} dh &= -\alpha \int_{0}^{t_f} dt\\
[\mu h^{1/\mu} ]|_{h_0}^{h_f} &= -\alpha t_f\\
h_f^{1/\mu} - h_0^{1/\mu} &= -\frac{\alpha}{\mu} t_f
\end{align*}
$$

therefore, if $$h_f \rightarrow 0$$ and $$t_f \geq T$$, we have

$$
T \leq \frac{\mu}{\alpha} h_0^{1/\mu}
$$

---------------------------------------
## Theorem 2 [FxTS 1]

Taken from [Ref 2].

<div class="code-example" markdown="1">
Suppose there exists a positive definite, continuously differentiable, radially unbounded function $$V: R^n \rightarrow R$$ such that

$$
\dot V(x) \leq -\alpha_1 V(x)^{\gamma_1} - \alpha_2 V(x)^{\gamma_2}, \quad \forall x \in R^n \backslash \{0\}
$$

where $$\alpha_1 > 0, \alpha_2 > 0$$, $$\gamma_1 >1$$, $$0 < \gamma_2 < 1$$.

Then the origin is *globally fixed time stable*, with settling time

$$
T \leq \frac{1}{\alpha_1(\gamma_1 - 1)} + \frac{1}{\alpha_2(1-\gamma_2)}
$$

By selecting $$\gamma_1 = 1 + 1/\mu$$ and $$\gamma_2 = 1 - 1/\mu$$, a (strictly) tigther upperbound on $$T$$ is

$$
T \leq \frac{\mu}{\sqrt{\alpha_1 \alpha_2}} \frac{\pi}{2}
$$

</div>

**Proof:**

The proof will be a special case of the next theorem.

<!-- We perform the same comparison system trick. Consider $$h(t)$$, where $$h(0) = V_0$$ and

$$
\dot h = -\alpha_1 h^{\gamma_1} - \alpha_2 h^{\gamma_2}
$$

Which can be separated and integrated

$$
\int_{h_0}^{h_f} \frac{1}{-\alpha h^\gamma_1 - \alpha_2 h^\gamma_2} dh = \int_{0}^{t_f} dt
$$ -->



---------------------------------------
## Theorem 3 [FxTS 2]

Taken from [Ref 2].

<div class="code-example" markdown="1">

Let $$V: R^n \rightarrow R$$ be a continuously differentiable, positive definite, radially unbounded function satisifying

$$
\dot V(x) \leq -\alpha_1 V(x)^{1+1/\mu} - \alpha_2 V(x)^{1-1/\mu} + \delta_1 V(x), \quad \forall x \in R^n \backslash \{0\}
$$

where $$\alpha_1 > 0, \alpha_2 >0 , \mu > 1, \delta_1 \in R$$.

Then there exists a neighborhood $$D \subset R^n$$ of the origin such that 

- If $$\frac{\delta_1}{2 \sqrt{\alpha_1 \alpha_2}} \leq 0$$, the origin is globally fixed time stable, (i.e., $$D = R^n$$) with settling time

$$
T \leq \frac{\mu}{\sqrt{\alpha_1 \alpha_2}} \frac{\pi}{2}
$$


- If $$0 \leq \frac{\delta_1}{2 \sqrt{\alpha_1 \alpha_2}} < 1$$, the origin is globally fixed time stable, (i.e., $$D = R^n$$) with settling time

$$
T \leq \frac{\mu}{\alpha_1 k_1} \left( \frac{\pi}{2} - \tan^{-1} k_2 \right)
$$

- If $$\frac{\delta_1}{2 \sqrt{\alpha_1 \alpha_2}} \geq 1$$, the origin is fixed time stable from a domain

$$
D = \left\{ x : V(x)^{1/\mu} \leq k \left(\frac{\delta_1 - \sqrt{\delta_1^2 - 4 \alpha_1 \alpha_2}}{2 \alpha_1}\right)\right\}
$$

with settling time

$$
T = \frac{\mu}{\alpha_1 (b-a)} \left( \log \left(\frac{b-ka}{a-ka}\right)- \log\left(\frac{b}{a}\right)\right) 
$$

where $$k \in (0, 1)$$ can be chosen arbitrarily, and $$a < b$$  are the solutions of $$\alpha_1 z^2 - \delta_1 z + \alpha_2 = 0$$ and $$k_1 = \sqrt{\frac{4 \alpha_1 \alpha_2 - \delta_1^2}{4 \alpha_1^2}}$$ and $$k_2 = \frac{-\delta_1}{\sqrt{4 \alpha_1 \alpha_2 - \delta_1^2}}$$

</div>


I will try to simplify this theorem, purely algebraically.

---------------------------------------
## Theorem 4  [FxTS 3]

<div class="code-example" markdown="1">

Let $$V: R^n \rightarrow R$$ be a continuously differentiable, positive definite, radially unbounded function satisifying

$$
\dot V(x) \leq -\alpha V(x)^{1+1/\mu} - \alpha V(x)^{1-1/\mu} + 2 \alpha r V(x), \quad \forall x \in R^n \backslash \{0\}
$$

where $$\alpha > 0, \mu > 1, r \in R$$.


Then there exists a neighborhood $$D \subset R^n$$ of the origin such that 

- If $$r \leq 0$$, the origin is globally fixed time stable, (i.e., $$D = R^n$$) with settling time

$$
T \leq \frac{\mu \pi}{2 \alpha}
$$


- If $$0 \leq r < 1$$, the origin is globally fixed time stable, (i.e., $$D = R^n$$) with settling time

$$
T \leq \frac{\mu}{\alpha \sqrt{1 - r^2}} \left( \frac{\pi}{2} + \tan^{-1} \left(\frac{r}{\sqrt{1 - r^2}}\right) \right)
$$

- If $$r \geq 1$$, the origin is fixed time stable from a domain

$$
D = \left\{ x : V(x)^{1/\mu} \leq k \left(r - \sqrt{ r^2 - 1}\right)\right\}
$$

with settling time

$$
T = \frac{\mu}{2 \alpha \sqrt{r^2-1}} \log \left(\frac{2 k}{1-k} r \left(\sqrt{r^2-1}-r\right) + \frac{1+k}{1-k}\right)
$$

where $$k \in (0, 1)$$ can be chosen arbitrarily. 

</div>

Notice that larger $$k$$ will have larger domains of attraction but larger settling times. As $$k\rightarrow 1$$, $$T\rightarrow \infty$$. As $$k\rightarrow 0$$, $$T \rightarrow 0$$.

