---
layout: default
title:  "Minimum Snap Trajectory Generation"
date:   2021-04-13
categories: controls theory math
parent: Notes on Control Theory
math: katex
---


# Minimum Snap Trajectory Generation

This set of notes details the mathematics and implementation of trajectory generation using minimum snap. 
While there are a number of really good references out there on how to do this, I had a lot of trouble actually implementing it, since many papers were rather vague.

## Motivation

The primary motivation is to design trajectories for quadrotors. Since the quadrotors dynamics are given by ([Ref 1])

$$
\begin{align*}
\dot x &= v\\
m\dot v &= mg e_3 - f R e_3 \\
\dot R &= R \hat{\omega}\\
J \dot \omega &= M - \omega \times J \omega
\end{align*}
$$

and are rather nonlinear and hard to work with, differential flatness can be very useful. 

The idea behind differential flatness is beyond the scope of this article, but it ultimately means that if we have a sufficiently smooth trajectory $x(t), y(t), z(t), \psi(t)$ (where $\psi$ is the yaw angle) then the quadrotor can be controlled using a suitable tracking controller (eg. the one in [Ref 1]). 

So, if we want to control our quadrotor to go through some waypoints, we need to generate a sufficiently smooth trajectory through the waypoints. 

See ([Ref 2]) for a clearer and more complete motivation and description of the idea.



## Problem Setup

Suppose we are provided 
-  a set of $$N+1$$ waypoints $$[w_0, w_2, ... w_N]$$, where each waypoint $$w_i \in \mathbb{R}$$
- the set of times to hit each waypoint $$[t_0, t_1, ..., t_N]$$, such that $$t_{i} < t_{i+1}$$.

We now need to design a trajectory that passes through each waypoint, at the right time, but also want to minimise the overall snap of the trajectory.

Mathematically, the optimal trajectory is a continuous and sufficiently differentiable curve $$P : [t_0, t_N] \to \mathbb{R}$$, to minimise

$$
J = \int_{t_0}^{t_N} (P^{(r)} (t))^2 dt
$$

where $$r$$ is the derivative order we are trying to minimise. When trying to minimise velocity between waypoints $$r = 2$$. Similarly $$r=3$$ to minimise acceleration, $$r=4$$ to minimize snap. 

In the case of a quadrotor, we aim to minimise the snap for the position components of the trajectory, and to minimize acceleration for the yaw component of the trajetory. 

## Basic Solution

Using the Euler-Lagrange equations, it is possible (see [Ref 2]) to show that the optimal solution to the above equations is a polynomial trajectory, and in particular a polynomial of degree $$2r - 1$$.

So, we can limit our search to polynomials. As I have noted in an earlier tech note, polynomial trajectories are very nice because you can define a vector space containing all polynomials of a given order, enabling us to use the tools of linear  algebra on polynomials. 

For this application, we choose the basis set:

$$
\{ 1, t, t^2, ..., t^{2r-1} \}
$$

and therefore, our search is over vectors in a $$2r$$-dimensional space, for each segment of the trajectory.

To be clearer, we are provided with $$N+1$$ waypoints, and therefore, we say that there are $$N$$ polynomial segments to optimize over, and thus the overall trajectory is given by

$$
P(t)  = \begin{cases} 
P_1(t) & \text{for } t_0 \leq t < t_1\\
P_2(t) & \text{for } t_1 \leq t < t_2\\
...\\
P_{N}(t) & \text{for } t_{N-1} \leq t < t_N
\end{cases}
$$

where each $$P_i(t)$$ is given by

$$
\begin{align*}
P_i(t) &= \sum_{l=0}^{n-1}  (t-t_{i-1})^i p_i^l\\
& = \begin{bmatrix} 1 & (t-t_{i-1}) & (t-t_{i-1})^2 & ... & (t-t_{i-1})^{n-1} \end{bmatrix} \begin{bmatrix} p_i^0 \\ p_i^1 \\ p_i^2 \\ \vdots \\ p_i^{n-1} \end{bmatrix}\\
&= b^T p_i
\end{align*}
$$


where $$b = b(t-t_i)\in \mathbb{R}^n$$.

Thus, for $$N$$ segments, if each is a polynomial of order $$n-1$$, there are $$n \cdot N$$ coefficients to be determined.


It is possible to show that the cost function can be expressed in the following way:

$$
\begin{align*}
J &= \int_{t_0}^{t_N} (P^{(r)} (t))^2 dt \\
&= \begin{bmatrix} p_1^T & p_2^T & ...&  p_{N-1}^T\end{bmatrix} \begin{bmatrix} Q_1 \\ & Q_2 \\ & & \ddots \\ & & & Q_{N-1}\end{bmatrix} \begin{bmatrix} p_1\\p_2\\ \vdots\\ p_{N-1}\end{bmatrix}\\
&= p^T Q p
\end{align*}
$$

where $$Q_i$$ is an appropriate matrix that depends on the segment duration $$t_{i+1} - t_{i}$$. Notice that this is a quadratic cost function, and it is also directly obvious that each $$Q_i$$ is a symmetric positive-definite matrix, and therefore $$Q$$ is also a symmetric positive definite matrix. In this way, the search over polynomial functions has been reduced to a search over real vectors.

However, we need to impose certain constraints on the polynomials:

- Waypoints: 

$$
P_i(t_i) = x_i \ \text{ and } P_i(t_{i+1}) = x_{i+1}\\
 \text{for } i= {0, 1, ..., N-1}
$$

- Continity and Smoothness at intermediate points

$$
P_i^{(r)}(t_{i+1}) = P_{i+1}^{(r)}(t_{i+1}) \\
 \text{for } i= {0, 1, ..., N-2}
$$

- Continuity and Smoothness at start and end points

$$
P_0















## References
1. T. Lee, M. Leok and N. H. McClamroch, "Geometric tracking control of a quadrotor UAV on SE(3)," 49th IEEE Conference on Decision and Control (CDC), 2010, pp. 5420-5425, doi: 10.1109/CDC.2010.5717652.
2. D. Mellinger and V. Kumar, "Minimum snap trajectory generation and control for quadrotors," 2011 IEEE International Conference on Robotics and Automation, 2011, pp. 2520-2525, doi: 10.1109/ICRA.2011.5980409.
