---
layout: default
title:  "Bayesian Filtering"
date:   2024-08-11
categories: controls theory math
parent: Notes on Control Theory
math: katex
---


# Bayesian Filtering

and how it connects to Kalman Filtering. 


## Preliminaries

Let $$P(E)$$ denote the probability of event $$E$$ occuring. The probability of multiple events $$E_1, E_2, ...$$ occuring simultaneously is denoted $$P(E_1, E_2, ...)$$. 

The probability of $$E_1$$ occuring given that $$E_2$$ occured is denoted $$P(E_1 \| E_2)$$. 

This notation can easily be extended. For example $$P(E_1, E_2 \| E_3) = P(E_1 , (E_2 \| E_3))$$ should be read as the probability of ($$E_1$$ occuring) and ($$E_2$$ occuring given $$E_3$$).

For a continuous random variable $$X$$, we write 

$$
F(x) = P(X \leq x)
$$

where $$F : \mathbb{R} \to \mathbb{R}$$ is the _cummulative distribution_ of $$X$$, and 

$$
f(x) = \lim_{\delta \to 0} P(x \leq X \leq x + \delta) = \frac{dF}{dx}(x)
$$

where $$f: \mathbb{R} \to \mathbb{R}$$ is the _probability density function_ of $$X$$. 

## Bayes' Rule:

For any two events $$A, B$$, 

$$
P(A, B) = P(A\|B) P(B)
$$

This can be used to derive the following:

$$
P(A\|B) = \frac{P(A, B)}{P(B)} = \frac{P(B, A)}{P(B)} = \frac{P(B\|A) P(A)}{P(B)}
$$

## Chain Rule:

For any three events $$A, B, C$$, 

$$
P(A, B, C) = P(A\|B, C) P(B\|C) P(C)
$$



## Bayesian Inference:

The goal is to determine the distribution

$$
p(x_t |\ y_{1:t})
$$ 

i.e., the distribution of the state $$x_t$$ at time $$t$$ conditioned on all the measurements $$y_{1:t}$$ obtained upto and including time $$t$$. 

We assume that we have access to 

- $$p(y_t \| x_t)$$ i.e., the distribution of the output conditioned on the state, and 
- $$p(x_{t-1} \| y_{1:t-1})$$ i.e., the distribution at the previous timestep, and 
- $$p(x_t \| x_{t-1})$$ i.e., a prediction model for the distibution of new state given the prior state. 

Further, we make the following assumptions:
- $$y_t$$ is only dependent on $$x_t$$
- $$x_t$$ is only dependent on $$x_{t-1}$$


Then, we can write the two steps of inference: 

### Prediction

Notice, 

$$
\begin{align}
p(x_t \| y_{1:t-1}) & \stackrel{(a)}{=} \int p(x_t, x_{t-1} \| y_{1:t-1}) dx_{t-1}\\
&\stackrel{(b)}{=} \int p(x_t \| x_{t-1}, y_{1:t-1}) p(x_{t-1} | y_{1:t-1}) dx_{t-1}\\
&\stackrel{(c)}{=} \int p(x_t \| x_{t-1}) p(x_{t-1} \| y_{1:t-1}) dx_{t-1}
\end{align}
$$

where
- (a) makes the dependence on $$x_{t-1}$$ explicit
- (b) follows from $$P(A, B) = P(A\|B)P(B)$$, and 
- (c) is because $$x_t$$ is independent of $$y_{1:t-1}$$. 


### Correction

When the $$k$$-th measurement is available, we can now update the posterior distribution:

$$
\begin{align}
p(x_t \| y_{1:t}) 
&\stackrel{(a)}{=}\frac{p(x_t, y_{1:t})}{p(y_{1:t})}\\ 
&\stackrel{(b)}{=}\frac{p(x_t, y_t, y_{1:t-1})}{p(y_{1:t})}\\
&\stackrel{(c)}{=}\frac{p(y_t, x_t, y_{1:t-1})}{p(y_{1:t})}\\ 
&\stackrel{(d)}{=}\frac{p(y_t \| x_t) p(x_t \| y_{1:t-1}) p(y_{1:t-1})}{p(y_{1:t})}\\ 
&\stackrel{(e)}{=}\frac{p(y_t \| x_t) p(x_t \| y_{1:t-1}) p(y_{1:t-1})}{p(y_t, y_{1:t-1})}\\ 
&\stackrel{(f)}{=}\frac{p(y_t \| x_t) p(x_t \| y_{1:t-1}) p(y_{1:t-1})}{p(y_t|y_{1:t-1}) p(y_{1:t-1})}\\
&\stackrel{(g)}{=}\frac{p(y_t \| x_t) p(x_t \| y_{1:t-1}) }{p(y_t|y_{1:t-1})}\\
&\propto p(y_t \| x_t) p(x_t \| y_{1:t-1})
\end{align}
$$

where 
- (a) is Bayes' rule
- (b) makes explicit that $$p(y_{1:t}) = p(y_{t}, y_{1:t})$$
- (c) reorders the terms in the numerator
- (d) applies Chain Rule
- (e) expands the denominator using the same property as in (b)
- (f) applies Bayes' rule to the denominator
- (g) cancels the $$p(y_{1:t-1})$$ term from the numerator and denominator

## Connection to Kalman Filtering

In Kalman Filtering, the filter maintains the state of $$\hat x$$ and $$P$$, defined in terms of the distributions of $$x$$, the random variable that is the state.

We assume the system model

$$
\begin{align}
x_{t} &= Fx_{t-1} + G w_{t-1}\\
y_{t} &= H x_t + v_t
\end{align}
$$

where $$w_{t} \sim \mathcal{N}(0, Q)$$, $$v_t \sim \mathcal{N}(0, R)$$. 

Define $$\hat x_{t\|t}$$, $$P_{t\|t}$$, $$\hat x_{t-1\|t}$$ and $$P_{t\|t-1}$$ such that 

$$
\begin{align}
\hat x_{t-1|t-1} &= \mathbb{E}[ x_{t-1} \| y_{1:t-1}]\\
\hat x_{t|t-1} &= \mathbb{E}[ x_{t} \| y_{1:t-1}]\\
\hat x_{t|t} &= \mathbb{E}[ x_{t} \| y_{1:t}]
\end{align}
$$

$$
\begin{align}
P_{t-1|t-1} &= \mathbb{V}[ x_{t-1} \| y_{1:t-1}]\\
P_{t|t-1} &= \mathbb{V}[ x_{t} \| y_{1:t-1}]\\
P_{t|t} &= \mathbb{V}[ x_{t} \| y_{1:t}]
\end{align}
$$

where $$\mathbb{E}[x]$$ is the expected value of $$x$$, and $$\mathbb{V}[x]$$ is the variance of $$x$$. 

Then, we must have

$$
p(x_{t-1} \| y_{1:t-1}) = \mathcal{N}( \mathbb{E}[x_{t-1} \| y_{1:t-1}], \mathbb{V}[x_{t-1} \| y_{1:t-1}]) = \mathcal{N}(\hat x_{t-1\|t-1}, P_{t-1\|t-1})
$$

$$
p(x_{t} \| y_{1:t-1}) = \mathcal{N}( \mathbb{E}[x_{t} \| y_{1:t-1}], \mathbb{V}[x_{t} \| y_{1:t-1}]) = \mathcal{N}(\hat x_{t\|t-1}, P_{t\|t-1})
$$

$$
p(x_{t} \| y_{1:t}) = \mathcal{N}( \mathbb{E}[x_{t} \| y_{1:t}], \mathbb{V}[x_{t} \| y_{1:t}]) = \mathcal{N}(\hat x_{t\|t}, P_{t\|t})
$$


Now, we are given $$\hat x_{t-1\|t-1}$$,  $$P_{t-1\|t-1}$$, and the measurement $$y_{t}$$,  and the goal is to determine $$\hat x_{t\|t}$$ and $$P_{t\|t}.$$

### Prediction

We can directly apply the Bayesian rules established above:

$$
\begin{align}
p(x_{t} \| y_{1:t-1}) &= \int p(x_t \| x_{t-1}) p(x_{t-1} \| y_{1:t-1}) d x_{t-1}\\
&= \int p((F x_{t-1} + G w_{t-1}) \| x_{t-1}) p(x_{t-1} \| y_{1:t-1}) d x_{t-1}\\  
&= \int (p((F x_{t-1}) \| x_{t-1}) + p((G w_{t-1}) \| x_{t-1})) p(x_{t-1} \| y_{1:t-1}) d x_{t-1}
\end{align}
$$

Now notice
$$
\begin{align}
p((F x_{t-1}) \| x_{t-1}) &= \frac{p(F x_{t-1}, x_{t-1})}{p(x_{t-1})}\\
&= 
\end{align}
$$