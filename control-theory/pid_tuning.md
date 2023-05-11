---
layout: default
title:  "Tuning a PID controller"
date:   2023-05-10
categories: controls theory math
parent: Notes on Control Theory
math: katex
---

# Tuning a PID Controller (or why linear systems theory is incredible)


## Setup

Consider a double integrator:

$$
\begin{align*}
\dot x_1 &= x_2\\
m\dot x_2 &= u
\end{align*}
$$

where the object has a mass $$m$$, and we apply the control input $$u$$. 

Suppose a PD controller is used to control this system:

$$
u = k_x (x_{ref} - x_1) + k_v (v_{ref} - x_2)
$$

Assuming $$v_{ref} = 0$$, the closed-loop system has a transfer function 

$$
\frac{X_1}{X_{ref}} = H(s) = \frac{k_x}{m s^2 + k_v s + k_x}
$$

This makes it a second order linear system, of the form 

$$
H(s) = \frac{\omega_n^2}{s^2 + 2 \xi \omega_n s + \omega_n^2}
$$

using the map:

$$
\begin{align*}
\omega_n = \sqrt{\frac{k_x}{m}},  \quad && \xi = \frac{k_v}{2} \sqrt{\frac{1}{m k_x}}
\end{align*}
$$

## Analysis
We can now analyze a few things about this system:

*  the **closed loop response** (i.e. solution to $$x_1(t)$$ such that $$x_1(0) = 0, x_2(0) = 0, x_{ref}(t) = 1$$) is 

$$
x_1(t) = 1 - e^{-\sigma t} \left( \cos(\omega_d t) + \frac{\sigma}{\omega_d} \sin(\omega_d t) \right)
$$

where $$\sigma = \xi \omega_n$$ and $$\omega_d = \omega_n \sqrt{1 - \xi^2}$$. 

*   the **rise time** (defined as the first time that $$x_1(t) = 1$$):

$$
t_r = \frac{2 \tan^{-1}{\left( \sqrt{ \frac{1 + \xi}{1 - \xi} } \right)}} {\omega_n \sqrt{1 - \xi^2}}
$$

*  the **overshoot** (defined as the maximum value reached minus one):

$$
M = \exp{\left(- \frac{\pi \xi}{\sqrt{1 - \xi^2}}\right)}
$$

There are other properties you can analyse, see Ref 1. 

## Design

Notice that there are two design parameters in the controller: $$k_x, k_v$$. 

Therefore, if the overshoot and the rise time are specified, the controller gains $$k_x, and k_v$$ can be determined, as follows:

1) Using the overshoot formula, determine $$\xi$$:

$$
\xi = \frac{- \log M}{\sqrt{\pi^2 + (\log M)^2}}
$$


2) Using the rise time, choose $$\omega_n$$:

$$
\omega_n = \frac{2}{t_r} \frac{\tan ^{-1}\left(\sqrt{\frac{1 + \xi}{1-\xi }}\right)}{\sqrt{1-\xi ^2} }
$$

3) Using $$\omega_n$$, determine $$k_x$$:

$$
k_x = m \omega_n^2
$$

4) Using $$\xi$$, determine $$k_v$$:

$$
k_v = 4 \xi^2 \sqrt{m k_x}
$$



## Examples

Matlab Code:

```matlab
%% inputs
m = 1.0;   % mass of system
M  = 0.10; % overshoot
tr = 0.4;  % rise time

%% outputs
xi = -(log(M)/sqrt(pi^2 + (log(M))^2))
omega_n = (2* atan(sqrt((1 + xi)/(1 - xi))))/(sqrt(1 - xi^2) * tr )
kx = m * omega_n^2
kv = 4 * xi^2 * sqrt(m * kx)
```

For $$m = 1.0$$ kg:

$$k_x$$: 

|     | 100 ms   | 200 ms   | 500 ms   | 1 s      | 2 s      |
|-----|----------|----------|----------|----------|----------|
| 1%  | 2.04E+03 | 5.09E+02 | 8.14E+01 | 2.04E+01 | 5.09E+00 |
| 5%  | 1.04E+03 | 2.60E+02 | 4.15E+01 | 1.04E+01 | 2.60E+00 |
| 10% | 7.46E+02 | 1.87E+02 | 2.98E+01 | 7.46E+00 | 1.87E+00 |
| 20% | 5.28E+02 | 1.32E+02 | 2.11E+01 | 5.28E+00 | 1.32E+00 |
| 50% | 3.35E+02 | 8.38E+01 | 1.34E+01 | 3.35E+00 | 8.38E-01 |

$$ k_v$$:

|     | 100 ms   | 200 ms   | 500 ms   | 1 s      | 2 s      |
|-----|----------|----------|----------|----------|----------|
| 1%  | 1.23E+02 | 6.16E+01 | 2.46E+01 | 1.23E+01 | 6.16E+00 |
| 5%  | 6.14E+01 | 3.07E+01 | 1.23E+01 | 6.14E+00 | 3.07E+00 |
| 10% | 3.82E+01 | 1.91E+01 | 7.64E+00 | 3.82E+00 | 1.91E+00 |
| 20% | 1.91E+01 | 9.55E+00 | 3.82E+00 | 1.91E+00 | 9.55E-01 |
| 50% | 3.40E+00 | 1.70E+00 | 6.80E-01 | 3.40E-01 | 1.70E-01 |

Here are a few starting points for PID tuning:

| $$M$$ | $$t_r$$ | $$k_x$$ | $$k_v$$ |
|-----|------|--------|--------|
| 0.25 | 0.5 | 18.8 | 2.83 | 
| 0.25 | 1.0 | 4.714 | 1.42 | 
| 0.125 | 0.5 | 26.7 | 6.29| 
| 0.125 | 1.0 | 6.68 | 3.14 | 


As a rough gauge of the control input required, the peak control input is likely to be at the very start, where the control input is $$u = k_x (x_{ref} - x) + k_v (v_{ref} - v) = k_x$$.
So by looking at the $$k_x$$ term, we can get a sense of the order of magnitude of the control input for a step input. 



## References:

1. https://courses.engr.illinois.edu/ece486/fa2019/handbook/lec06.html
