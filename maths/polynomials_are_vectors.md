---
layout: default
title:  "Polynomials are Vectors"
date:   2021-04-13
categories: math
parent: Interesting Maths
math: katex
---

# Polynomials are Vectors

A polnomial can be represented as a vector, using the notion of basis vectors. 

For example, if we define the set of basis 'vectors':

$$
b_0(s) = 1\\
b_1(s) = s\\
b_2(s) = s^2
$$

and we wanted to represent the polynomial

$$
p(s) = 1 + 2s - 5s^2
$$

we can write 

$$
p(s) = \begin{bmatrix} b_0(s) & b_1(s) & b_2(s) \end{bmatrix}\begin{bmatrix} 1 \\ 2 \\-5\end{bmatrix}
$$

and thefore, when the polynomial $$p(s)$$ is represented in the basis $$B = [b_0(s), b_1(s), b_2(s)]$$, we can say that 

$$
\big[p(s)\big]_{B} = \begin{bmatrix} 1 \\ 2 \\-5\end{bmatrix}
$$

This notation thus makes a bunch of operations really easy!

For instance when adding polynomials, we only need to add the vector representations. 

When multiplying two polynomials, say 

$$r(s) = p(s) q(s) = (B u) ( B v) = u^T B^T B v $$

if $$u, v$$ are the vectors representing $$p(s)$$ and $$q(s)$$ respectively. Notice that the inner matrix $$B^T B$$ can be precomputed, and now, mulitplying two new polynomials together can be very easy!

Also notice that we can use many other basis vectors: they just need to be a basis ( i.e., a linearly independent set of vectors that span the vector space). 

For instace, we could have chosen 
$$C = \begin{bmatrix}1 + s, & s^2 -1, & 2s^2 + 8 \end{bmatrix}$$ 
as a basis, and still represented the polynomial above. 

We need 

$$
p(s) = 1 + 2 s - 5 s^2 = \alpha_1 (1+s) + \alpha_2 (s^2 - 1) + \alpha_3(2 s^2 + 8)
$$

therefore, by comparing coefficients, we get three equations:

$$
\begin{align*}
1 &= \alpha_1 - \alpha_2 + 8 \alpha_3 \quad [\text{by coefficient of } s^0]\\
2 &= \alpha_1 \\
-5&= \alpha_2 + 2 \alpha_3
\end{align*}
$$

which we can solve for:

$$\alpha_1 = 2, \quad \alpha_2 = -3.8, \quad \alpha_3 = -0.6$$

and so $$p(s)$$ is represented in $$C$$ as

$$
p(s) = C \begin{bmatrix} 2 \\ -3.8 \\ -0.6 \end{bmatrix}
$$

$$
\therefore \big[p(s)\big]_{C} = \begin{bmatrix} 2 \\ -3.8 \\ -0.6 \end{bmatrix}
$$

Ive barely scratched the surface of what can happen with such representatsion but I just thought this was cool!
