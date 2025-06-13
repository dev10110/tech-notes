---
layout: default
title:  "Paradoxical Proofs"
date:   2025-06-13
categories: math
parent: Interesting Maths
math: katex
---

# Paradoxical Proofs

This page collects a few "theorems" that are obviously absurd, but for which a proof seemingly can be written up.
While mostly amusing, the goal is to demonstrate the ease with which one can construct "proofs" that are actually false. 
I strongly encourage you to try to find the flaw in the proof before opening the hint.

I will keep adding new fake proofs as I find them. 

## $$N=1$$ is the largest integer. 

{: .note-title }
> Theorem:
> 
> The largest integer is 1.


{: .highlight-title }
> Proof:
> 
> Let $$N$$ be the largest integer, and suppose $$N \neq 1$$, and $$N \neq 0$$. Then, $$N^2 > N$$ and therefore $$N$$ is not the largest integer. Since $$1 > 0$$, $$N=1$$ is the largest integer. 

<details markdown="block">
<summary>
Where's the flaw?
</summary> 
[Liberzon](https://liberzon.csl.illinois.edu/teaching/cvoc/node89.html) states it quite succinctly: 
"This argument is known as Perron's paradox. Although clearly farcical, it highlights a serious issue which we have been dodging up until now: it warns us about the danger of assuming the existence of an optimal solution. Indeed, finding the largest positive integer is an optimization problem. Of course, a solution to this problem does not exist. In the language of this book, the above reasoning correctly shows that $$N=1$$ is a necessary condition for optimality. Thus a necessary condition can be useless--even misleading--unless we know that a solution exists.

We know very well that the maximum principle only provides necessary conditions for optimality. The same is true for the Euler-Lagrange equation and several other results that we have derived along the way. We have said repeatedly that fulfillment of necessary conditions alone does not guarantee optimality. However, the basic question of whether an optimal solution even exists has not been systematically addressed yet, and it is time to do it now."
</details>

## There is only one email address. 

{: .note-title}
> Theorem:
> 
> All people have have the same email address. 

{: .highlight-title}
> Proof: 
> 
> We will prove this using induction. 
> 
> Suppose the induction claim is that any group of $$N$$ people have the same email address; we will now prove that this statement must hold true. 
> 
> Base Case: In a group of $$N=1$$ people, obviously all people have the same email address. 
>
> Induction step: Fix $$k$$. Suppose any set of $$k$$ people have the same email address by the inductive hypothesis. Consider now any set of $$k+1$$ people. Then, by the inductive hypothesis, people 1 to $$k$$ have the same email address. There is only one person left to check. But we also know that people 2 to $$k+1$$ have the same email address by the inductive hypothesis. Thus they all have the same email address. 
> 
> In this way, by induction, we know that any group of $$k$$ people must have the same email address. Since there are a (countably) finite number of people, they must all have the same email address. 

<details>
<summary> Wheres the flaw? </summary>
The flaw is that the induction step only holds true for $$k>=2$$, and does not apply to $$k=1$$. Hence, although the base case is true, and the induction step is true, we cannot apply the induction step to the base case. This example highlights a common flaw in induction, where people forget to show that the induction step also holds for the base case. 
</details>
