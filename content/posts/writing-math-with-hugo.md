---
title: 'Writing Math With Hugo'
date: '2024-03-26T09:08:10+11:00'
math: 'katex'
---

I initially attempted to follow the setup of [mathematics in content management section of
Hugo](https://gohugo.io/content-management/mathematics/). Not knowing how it worked, I assumed I can copy and paste
these configurations straight out of the box, but this did not work. I wanted to run and I wanted to run fast without first understanding the
underlying architecture of Hugo

In comes a bit of googling and running into this article by Misha Brukman in [Writing Math with
Hugo](https://misha.brukman.net/blog/2022/04/writing-math-with-hugo/). With a bit of copy + paste. Here are the results below.

{{< math >}}
$$
  \begin{bmatrix}
    a & b \\
    c & d \\
    e & f \\
  \end{bmatrix}
$$
{{< /math >}}

$$
\[
\begin{aligned}
KL(\hat{y} || y) &= \sum_{c=1}^{M}\hat{y}_c \log{\frac{\hat{y}_c}{y_c}} \\
JS(\hat{y} || y) &= \frac{1}{2}(KL(y||\frac{y+\hat{y}}{2}) + KL(\hat{y}||\frac{y+\hat{y}}{2}))
\end{aligned}
\]
]
$$

