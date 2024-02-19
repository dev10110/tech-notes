---
layout: default
title:  "Legendre Transform"
date:   2024-02-19
categories: math
parent: Interesting Maths
math: katex
---

# The Legendary Legendre Transform 

The Legendre transform is an incredible trick when it comes to convex analysis. I am documenting some interesting properties that have been useful in a surprisingly large number of contexts. We start by explaining conjugate functions, and then its connection to the Legendre Transform. While the Legendre Transform is only defined for convex differentiable functions, the convex conjugate is more general.

Most of this, of course, comes from Boyd's book on Convex Optimization (see Section 3.3). 

<iframe width="560" height="315" src="https://www.youtube.com/embed/lEN2xvTTr0E?si=xLoqzS40nbk3rCtb&amp;start=1639" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

[This page](../assets/pdfs/convex_conjugate.pdf) has some additional details, interpretations, and uses of the convex conjugate that is really good!


## Definition: Conjugate Function

Consider a function $$f: \mathbb{R} \to \mathbb{R}$$ that _may or may not_ be convex. The **conjugate function** is a new function  $$f^* : \mathbb{R} \to \mathbb{R}$$, 

$$
f^*(y) = \sup_{x \in \text{dom} f} \left ( y^T x - f(x) \right ).
$$


## Property 1:  Convexity

$$f^*$$ is convex (even if $$f$$ is not). 

_Proof:_
Since $$f^*$$ is the pointwise supremum of a family of affine (and therefore convex) functions of $$y$$. This is independent of whether or not $$f$$ is a convex function. Therefore, $$f^*$$ is a convex function. 

## Property 2: Fenchel's Inequality 

$$
f(x) + f^*(y) \geq x^T y
$$

This has also been called Young's inequality when $$f$$ is differentiable. 

## Property 3: Conjugate of a Conjugate

If $$f$$ is convex and closed, then $$f^* = f$$. 

The phrase "$$f$$ is closed" means that the epigraph of $$f$$ is a closed set. 


## Property 4: Scaling with Affine Transforms

For nonsingular $$A \in \mathbb{R}^{n \times n}$$  and $$b \in \mathbb{R}$$, let

$$
g(x) = f(A x + b)
$$

Then the conjugate of $$g$$ is 

$$
g^*(y) = f^*(A^{-T} y) - b^T A^{-T} y
$$

with $$\text{dom} g^* = A^T \text{dom} f^*$$. 

## Property 5: Sums of independent functions 
Let

$$
f(u, v) = f_1(u) + f_2(v)
$$

where $$f_1, f_2$$ are convex. Then 

$$
f^*(w, z) = f_1^*(w) + f_2^*(z).
$$

Therefore as long as $$f$$ is a separable sum of convex functions, the conjugate of $$f$$ is also separable in the same way. 

## Property 6: Convex Envelopes

The conjugate of a conjugate can be used to construct the "convex envelope" of a function. 

$$
f^{env} = (f^*)^*
$$

meaning, the convex envelope function $$f^{env}(x)$$, (i.e. the largest convex function that lies below $$f(x)$$ for all $$x$$), is the conjugate of the conjugate of $$f$$. 

_Proof:_

$$
\text{epi} \left ( f^{env} \right ) = \text{conv} \left ( \text{epi} f \right )
$$

## Property 7: Lower Bounds

$$
f^*(0) = - \inf_{x} f(x)
$$

_Proof:_ Just plug in the definition and cancel signs. 



## Definition: Legendre Transform
Suppose $$f$$ is convex and differentiable, with $$\text{dom} f = \mathbb{R}^n$$. Then

$$
f^*(y) = z^T \nabla f(z) - f(z)
$$ 

where  $$y = \nabla f(z)$$. 

Incredible! Notice that the $$\sup_{x}( \cdot)$$ is gone! And we have effectively have a closed form expression for $$f^*$$ in terms of the gradients of $$f$$. 

_Proof:_ 
Since $$f$$ is convex and differentiable, we can say $$\text{dom} f = \mathbb{R}^n$$. Therefore any $$x^*$$ that maximizes $$y^T x - f(x)$$ must satisfy 

$$
\frac{\partial}{\partial x} y^T x^* - f(x^*) = 0 \implies y - \nabla f(x*) = 0
$$

Therefore, we must have

$$
\begin{array}{rl}
f^*(y) &= \sup_{x} ( y^T x - f(x) )\\
&= (\nabla f(x^*)^T x^* - f(x^*)\\
&= x^{*T} \nabla f(x^*) - f(x^*)\\
&= z \nabla f(z) - f(z)
\end{array}
$$

where $$y = \nabla f(z)$$, since we used the substitutions $$\{ x^* \to z, y \to \nabla f(x^*) \}$$. 


## Examples: 

See Section 3.3 of Boyd for the proofs. 

| $$f$$  | $$\text{dom} f^*$$ | $$ f^*$$ | 
|:----------|:-----------|:--------------|
| $$f(x) = ax + b$$ | $$\{ a \}$$ | $$ f^*(x) = -b$$ | 
| $$f(x) = -\log x$$ | $$ \{ y : y < 0 \} $$ | $$ f^*(y) = -\log (-y) - 1$$ | 
| $$f(x) = e^x$$ | $$ \{ y : y \geq 0 \} $$ | $$f^*(y) = y \log y - y$$, where $$0 \log 0 = 0$$ |
| $$f(x) = x \log x $$ | $$ \mathbb{R}$$ | $$f^*(y) = e^{y - 1}$$ | 
| $$f(x) = 1/x$$ | $$ \{ y : y \leq 0 \} $$ | $$f^*(y) = -2 \sqrt{-y} $$ | 
| $$f(x) = \frac{1}{2} x^T Q x$$, $$Q$$ is symm. pos. def. | $$ \mathbb{R^n}$$ | $$f^*(y) = \frac{1}{2} y^T Q^{-1} y$$ | 
| $$f(X) = \log \det X^{-1}$$, $$f : \mathbb{S}_{++}^n \to \mathbb{R} $$ | $$-\mathbb{S}_{++}^n = \{ Y : Y < 0 \}$$ | $$ f^*(Y) = \log \det (-Y)^{-1} - n$$ |
