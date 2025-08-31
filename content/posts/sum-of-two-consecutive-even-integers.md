---
title: 'Sum of squares of two consecutive even integers'
date: 2025-08-31T07:09:55+10:00
math: 'katex'
tags:
  - math
---
## Background

I was working through the practice problems found in *College Algebra Seventh Edition* by *Stewart, Redlin, and Watson*
when I stumbled upon this problem found in *1.4 Exercises, Question 68*. The question is stated as:

**The sum of the squares of two consecutive even integers is 1252. Find the integers.**

I found this question very enjoyable as:
  1. You needed to have a concrete definition of an **even integer**
  2. Solve the equation from your knowledge of **quadratic equations**

## Solving the equation

We can formally state the question as

{{< math >}}
$$
m^2 + n^2 = 1252\ 
\ and\ m, n \in \mathbb{Z}
$$
{{< /math >}}

where:

$m, n \in \mathbb{Z}$ ,  $m$ and $n$ are integers and

$m = n + 1$ ,  $m$ is the next consecutive integer after $n$
{{< math >}}
{{< /math >}}

We can define an even integer as provided by *Kenneth Rosen* in his Discrete Mathematics books. An integer $n$ is even
if for some integer $k$, $n = 2k$. We can intuitively see this by giving some examples of $k$.

When $k = { 1, 2, 3, 4, ... }$ then $n = { 2, 4, 6, 8, ... }$ and here we can see that for any $k$ if it is an even
integer, it will be found in $n$.

How does this help us out? Well, now we can substitute values for $m$, and $n$ by the definition of an integer. We see
that $m = 2k$ and $n = 2(k + 1) = 2k + 2$ where $k$ is the integer in question. Here we relate $m$ to $n$ as we know
that $n$ must be the next integer in sequence after $m$.

Substituting our updated definition of $m$ and $n$ into the equation we get.


{{< math >}}
$$
m^2 + n^2 = 1252
$$
$$
(2k)^2 + (2k + 2)^2 = 1252
$$
{{< /math >}}

Expanding the squares
{{< math >}}
$$
4k^2 + 4k^2 + 8k + 4 = 1252
$$
{{< /math >}}

Simplifying
$$
8k^2 + 8k - 1248 = 0
$$
$$
k^2 + k - 156 = 0
$$

We can now solve the quadratic equation for $k$ using a multitude of tools, but we can use the quadratic formula
for ease of computation. We see that the above simplified equation has the form $ax^2 + bx + c = 0$

Here: $a = 1,\ b = 1,\ c = -156$

Substituing into the quadratic formula:

$$
\frac{-b\pm \sqrt{b^2 - 4ac}}{2a}
$$
$$
k = \frac{-1\pm \sqrt{1^2 - 4(1)(-156)}}{2(1)}
$$
$$
k = \frac{-1\pm \sqrt{1 + 624}}{2}
$$
$$
k = \frac{-1\pm \sqrt{625}}{2}
$$
$$
k = \frac{-1\pm 25}{2}
$$
$$
\therefore\ k = 12,\ k = -13
$$

We have 2 solutions for $k$, and if we plug them into $m$, and $n$ we have.

For $k = 12$
$$
m = 2k = 2\times 12 = 24
$$
$$
n = 2k + 2 = 2\times 12 + 2 = 26
$$
$$
m^2 + n^2 = 24^2 + 26^ = 576 + 676 = 1252
$$

For $k = -13$
$$
m = 2k = 2\times -13 = -26
$$
$$
n = 2k + 2 = 2\times -13 + 2 = -24
$$
$$
m^2 + n^2 = 1252 = (-26)^2 + (-24)^2 = 676 + 575 = 1252
$$

From the above we can see that we have 2 solutions for the values of $m$ and $n$, that satisfies
the sum of squares of two consecutive positive integers.

For the positive integer solution
$$
m = 24,\ n = 26
$$

For the negative integer solution
$$
m = -26,\ n = -24
$$

## Disclaimer

None of this text has been created or proof read by any Generative AI. All of the content on this article has been
manually typed by me.
