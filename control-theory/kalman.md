---
layout: default
title:  "Kalman Filtering"
date:   2021-05-11
categories: controls theory math
parent: Notes on Control Theory
math: katex
---


# Kalman Filtering

Explained a bit more simply. 

## Set up

Consider a linear time-invariant dynamical system, written in discrete time:

$$
\begin{align*}
x_{k+1} &= Ax_k + B u_k + w_k\\
y_{k} &= C x_k + v_k
\end{align*}
$$

where 
- $$w_k \sim \mathcal{N}(0, Q)$$ is a zero-mean _process noise_ with covariance $$Q$$.
- $$v_k \sim \mathcal{N}(0, R)$$ is a zero-mean _measurement noise_ with covariance $$R$$.
- $$x_0 \sim \mathcal{N}(\hat{x}_0, P_{0, 0})$$ is the initial state, which is assumed to the normally distributed around $$\hat{x}_0$$ with covariance $$P_{0,0}$$.

For the KF to work, we must assume that the system $$(A, C)$$ is observable. 

Notation: 
- $$x_{k}$$ is the true state at time $$k$$.
- $$\hat{x}_{k,k}$$ is our best estimate of $$x_k$$, using all measurements upto and including time $$k$$. This is called the _posterior_ estimate.
- $$\hat{x}_{k+1, k}$$ is our best estimate of $$x_{k+1}$$, using all measurements upto and including time $$k$$. This is called the _prior_ estimate.
- $$P_{k,k} = \text{cov}(x_k - \hat{x}_{k,k})$$  is the covariance of the posterior estimate.
- $$P_{k+1,k} = \text{cov}(x_{k+1} - \hat{x}_{k+1,k})$$ is the covariance of the prior estimate.

## Final Equations

We will be generating an estimate of the state, in the following way:

1. Predict the state, in an open-loop sense
2. Correct the estimate, using the received measurements. 

Mathematically, 

$$
\hat{x}_{k+1, k} = A \hat{x}_{k,k} + B u_k\\
P_{k+1, k} = A P_{k,k} A^T + Q
$$

is a direct *prediction* of where the state should be based on the previous best estimate. The covariance of the prior is also updated.

Now, once the new measurement comes in, we have the ability to check how wrong the estimate is. We call this the _innovation_, $$i_{k+1}$$:

$$
i_{k+1} = y_{k+1} - C \hat{x}_{k+1, k}
$$

and so we *correct* the estimated state as

$$
\hat{x}_{k+1,k+1} = \hat{x}_{k+1, k} + K i_{k+1}\\
P_{k+1, k+1} = (I - K C) P_{k+1, k}
$$

by simplying taking the prior estimate, and shifting it, to better match the measurement. Notice, since the measurement is noisy, we don't shift it to perfectly match the noisy measurement. This proportion is controlled by $$K$$, the *Kalman Gain*, which is given by 

$$
K = P_{k+1, k} C^T (C P_{k+1, k} C^T + R)^{-1}
$$

which looks scary, but is actually quite straightforward, as demonstrated below. Also note, $$K$$ is changing at each time step. I have just dropped the subscript $$K_k$$ since it only gets in the way. 

The important thing to remember is what the Kalman gain is trying to optimize: it is trying to make $$P_{k+1, k+1}$$, the posterior state estimate covariance, as 'small' as possible. Rigourously speaking, it is trying to minimise the $$\text{trace}(P_{k+1, k+1})$$. 

In the rest of this page, we derive the above equations. We first need a bit of mathematical background, and then we derive the equations.

-----------------------------------------------------



## Useful Mathematical properties

There are a few mathematical properties that will be used in the proof. If you are familiar with them, feel free to skip this section. 

**Properties of Gaussian Random Vectors**

A vector $$w \in R^{n}$$ is a gaussian vector if it is sampled from a normal distribution. We write

$$
w \sim \mathcal{N}(\bar w, Q)
$$

where $$\bar w = E(w)$$ is the mean, and $$Q = \text{cov}(w)$$ is the covariance matrix, 

$$
Q = \text{cov}(w) = E[(w - \bar w) ( w - \bar w)^T] = E[w w^T ] - \bar w \bar w^T
$$

and is always square (of size $$n \times n$$), symmetric, positive-semi definite. Notice that *if* $$\bar w = 0$$, the covariance matrix simplifies to $$\text{cov}(w) = E[w w^T]$$

The covariance matrix also has the following properties, which we will be using:

- if $$A \in R^{n\times n}, b \in R^n$$ are constant matrix and vector,

$$
\text{cov}(A w + b) = A \text{cov}(w) A^T 
$$

- the negative sign does not affect the covariance:

$$
\text{cov}(-Aw) = A\text{cov}(w)A^T
$$

- but you have to be careful when subtracting:

$$
\text{cov}( Aw - B w) =  (A-B) \text{cov}(w) (A-B)^T
$$

**not** $$A \text{cov}(w) A^T + B \text{cov}(w) B^T = (A+B) \text{cov}(w) (A+B)^T$$.

- if (and only if) you have two uncorrelated random vectors $$w \sim \mathcal{N}(\bar w, Q), v\sim \mathcal{N}(\bar v, R)$$, then

$$
\text{cov}(v + w) = \text{cov}(v) + \text{cov}(w)
$$

- if the two random vectors *are* correlated (will not be used in this document, but useful to know), we have

$$
\text{cov}(v + w) = \text{cov}(v) + \text{cov}(w)  + \text{cov}(v, w) + \text{cov}(w, v)
$$

where $$\text{cov}(v, w) = E[(v - \bar v) ( w - \bar w)^T]$$. You can use this to show why the minus sign appears in $$\text{cov}(Aw-Bw)$$.

**Properties of Trace and Trace Derivatives**

We will be minimising the trace of a matrix, and so it is useful to know the following:

Consider two matrices $$A \in R^{m \times n}, B \in R^{n\times m}$$

$$\text{tr}(AB) =  \text{tr}(BA)$$

$$\text{tr}(A+B) =  \text{tr}(A) + \text{tr}(B)$$

$$
\frac{d}{dA} \text{tr}(A B) = \frac{d}{dA} \text{tr}(B A) = B^T
$$

$$
\frac{d}{dA} \text{tr}(A B A^T) = A ( B^T + B)
$$



## Derivation of the Kalman Filter Equations:

### Predictor Step Derivations

The prediction step involves an open-loop prediction of the new state, based on the dynamics of the system:

$$
\hat{x}_{k+1, k} = A \hat{x}_{k,k} + B u_k
$$

therefore, we can determine the prior estimation error:

$$
\begin{align*}
e_{k+1, k} &= x_{k+1} - \hat{x}_{k+1, k}\\
&= (A x_k + B u_k + w_k) - (A \hat{x}_{k,k} + B u_k)\\
&= A(x_k - \hat{x}_{k,k}) + w_k\\
&= Ae_{k, k} + w_k
\end{align*}
$$

therefore, the update of the prior covariance is 

$$
\begin{align*}
\text{cov}(e_{k+1, k}) &= \text{cov}(Ae_{k, k} + w_k)\\
P_{k+1, k} &= \text{cov}(Ae_{k, k}) + \text{cov}(w_k)\\
&= A P_{k,k} A^T + Q
\end{align*}
$$

where the first step used the assumption that the noise is uncorrelated with the state and error. Notice the $$APA^T$$ comes from the properties of random vectors.


### Corrector Step

Now the corrector step performs the following operation:

$$
\hat{x}_{k+1, k+1} = \hat{x}_{k+1, k} + K (y_{k+1} - C \hat{x}_{k+1, k})
$$


therefore, the posterior error is 

$$
\begin{align*}
e_{k+1, k+1} &= x_{k+1} - \hat{x}_{k+1, k+1}\\
&= x_{k+1} - \hat{x}_{k+1, k} - K (y_{k+1} - C \hat{x}_{k+1, k})\\
&= e_{k+1, k} - K (y_{k+1} - C \hat{x}_{k+1, k})\\
&= e_{k+1, k} - K ((C x_{k+1} + v_k) - C \hat{x}_{k+1, k})\\
&= e_{k+1, k} - K C (x_{k+1} - \hat{x}_{k+1, k}) - K v_k\\
&= e_{k+1, k} - K C e_{k+1, k} - K v_k\\
&= (I - K C) e_{k+1, k} - K v_k\\
\end{align*}
$$

and the covariance will be 

$$
\begin{align*}
\text{cov}(e_{k+1, k+1}) &= \text{cov}((I - K C) e_{k+1, k} - K v_k)\\
P_{k+1, k+1} &= \text{cov}((I - K C) e_{k+1, k})  + \text{cov}(-  K v_k)\\
 &= (I - K C) P_{k+1, k}(I - K C)^T  + K R K^T\\
 &= (I - K C) P_{k+1, k}(I - K C)^T  + K R K^T\\
\end{align*}
$$

which shows rather clearly that the posterior covariance is a weighted sum of the prior covariance and the  measurement noise! The weight is essentially the Kalman gain $$K$$.  

So how should we pick $$K$$? Ultimately we want a small posterior covariance, and so we want to choose a $$K$$ that minimizes $$P_{k+1, k+1}$$. Before we do this minimization, lets apply some intuition. 

Suppose, after the minimization, we have a large $$K$$. That means that we are not worried about the second term - the $$R$$ must be really small - therefore, the measurement noise is very small. Conversely, suppose $$K$$ was close to 0 - then we have $$P_{k+1, k+1} \approx P_{k+1, k}$$: we are ignoring the measurements, since the measurement noise is too large!

In this way, the 'magnitude' of $$K$$ is determined by whether or we trust the measurements (large $$K$$), or trust the open-loop dynamics (small $$K$$). 

Lets use calculus to determine the optimal $$K$$:

### Kalman Gain:

We wish to solve the following mathematical problem:

$$
\begin{array}{rl}
\underset{K}{\text{minimise}} & \text{tr}(P_{k+1, k+1})
\end{array}
$$

where the trace is used since it represents the sum of the eigenvalues of the matrix - correlated with the sum of the variances of each element in the vector. 

Lets first expand out $$P_{k+1, k+1}$$:

$$
\begin{align*}
P_{k+1, k+1} &= (I - K C) P_{k+1, k}(I - K C)^T  + K R K^T\\
&= (I - K C) (P_{k+1, k} - P_{k+1, k} C^T K ^T) + K R K^T\\
&= P_{k+1, k} - K C_k P_{k+1, k} - P_{k+1, k} C^T K ^T + K C P_{k+1, k} C^T K^T + K R K^T\\
&= P_{k+1, k} - K C_k P_{k+1, k} - P_{k+1, k} C^T K ^T + K (C P_{k+1, k} C^T  + R ) K^T
\end{align*}
$$

and so the trace is 

$$
\begin{align*}
\text{tr}(P_{k+1, k+1}) &= \text{tr}(P_{k+1, k} - K C_k P_{k+1, k} - P_{k+1, k} C^T K ^T + K (C P_{k+1, k} C^T  + R ) K^T)\\
&= \text{tr}(P_{k+1, k}) - \text{tr}(K C_k P_{k+1, k}) - \text{tr}(P_{k+1, k} C^T K ^T) + \text{tr}(K (C P_{k+1, k} C^T  + R ) K^T)\\
&= \text{tr}(P_{k+1, k}) - 2\text{tr}(K C_k P_{k+1, k}) + \text{tr}(K (C P_{k+1, k} C^T  + R ) K^T)
\end{align*}
$$

Therefore, we can take the derivative and set it to zero:

$$
\begin{align*}
\frac{d}{dK} \text{tr}(P_{k+1, k+1}) & = 0\\
\frac{d}{dK} \Big[\text{tr}(P_{k+1, k}) - 2\text{tr}(K C_k P_{k+1, k}) + \text{tr}(K (C P_{k+1, k} C^T  + R ) K^T) \Big] &= 0\\
\frac{d}{dK} \text{tr}(P_{k+1, k}) - 2 \frac{d}{dK} \text{tr}(K C_k P_{k+1, k}) + \frac{d}{dK}\text{tr}(K (C P_{k+1, k} C^T  + R ) K^T) &= 0\\
0 - 2  (C_k P_{k+1, k})^T + 2 K (C P_{k+1, k} C^T  + R ) &= 0\\
\end{align*}
$$

and therefore, solving for $$K$$ we have 

$$
K = P_{k+1, k} C^T (C P_{k+1, k}  C^T + R)^{-1}
$$

and this inverse will (almost) always exist, since $$R$$ is positive semi-definite, and $$C P C^T$$ is positive semi-definite, and symmetric. If it does not exist, the Kalman Filter cannot be used. This can be remedied by choosing $$R$$ to be positive definite, in which case the inverse always exists.

Finally, since we have found Kalman gain, we can substitute it back into the posterior covariance update, and notice some simplifications:


$$
\begin{align*}
P_{k+1, k+1} &= (I - K C) P_{k+1, k}(I - K C)^T  + K R K^T\\
&= (I - K C) (P_{k+1, k} - P_{k+1, k} C^T K^T)  + K R K^T\\
&= (I - K C) (P_{k+1, k} -  (I - K C) (P_{k+1, k} C^T K^T)  + K R K^T\\
&= (I - K C) P_{k+1, k} - P_{k+1, k} C^T K ^T + K C P_{k+1, k} C^T K^T + K R K^T\\
&= (I - K C) P_{k+1, k} - P_{k+1, k} C^T K ^T + K (C P_{k+1, k} C^T  + R)K^T \\
&= (I - K C) P_{k+1, k} - P_{k+1, k} C^T K ^T + P_{k+1, k} C^T K ^T\\
&= (I - K C) P_{k+1, k}
\end{align*}
$$


and thats it. Thats the derivation of the Kalman Filter equations. Hopefully this is less scary than whatever you had in the textbook. In essence we are simply propagating the covariance, based on the predictor and the corrector step. 


### Summary:

To summarise, here are the equations again, in the order you need to compute them:

$$
\boxed{
\begin{align*}
\hat{x}_{k+1, k} &= A \hat{x}_{k,k} + B u_k\\
P_{k+1, k} &= A P_{k,k} A^T + Q\\
K &= P_{k+1, k} C^T (C P_{k+1, k} C^T + R)^{-1}\\
\hat{x}_{k+1,k+1} &= \hat{x}_{k+1, k} + K (y_{k+1} - C \hat{x}_{k+1, k})\\
P_{k+1, k+1} &= (I - K C) P_{k+1, k}
\end{align*}
}
$$


If the matrices $$A, B, C, Q, R$$ are time varying, simply use the time-varing versions in the equations above.








## Closing Notes:

Technically, to prove the optimality of the kalman filter, you would need to determine why the predictor-corrector separation is possible and indeed optimal in the first place. I would refer to textbooks for that part of the derivation. 

Also, in the above derivation, we assumed that the A, B, C, Q, R matrices are perfectly well known. I wonder how this should be modified, if these matrices are uncertain too. My intuition says we can bound the errror of A, B, C and include it in the Q, R matrices, but I am not sure.
