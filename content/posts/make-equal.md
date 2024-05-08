+++
title = 'Make Equal'
date = 2024-04-09T22:40:37+10:00
tags = ['math', 'competitive programming']
draft = true
math = 'katex'
+++

I stumbled upon this problem practicing Competitive Programming on codeforces. The problem can be found
[here](https://codeforces.com/contest/1931/problem/B). Feel free to try it yourself :smile:.

What I like about this problem is it does not require any specialised math or algorithms and data structures knowledge.
It only requires some insight and understanding into what the question is truly asking.

## Quick summary of the problem.

There are $n$ containers of water lined up, numbered from left to right from $1$ to $n$. Each container can hold any
amount of water; initially the $i$-th container contains $a_i$ units of water. The sum of $a_i$ is divisible by $n$.

You can apply the following operation any (possibly zero) number of times: pour any amount of water from the $i$-th
container to the $j$-th container, where $i$ must be **less** than $j$ (i.e. $i<j$). Any index can be chosen as $i$ or
$j$ any number of times.

Determine whether it is possible to make the amount of water in all containers the same using this operation.

### Input
First line of input contains a single integer $t$ where $(1\leq t \leq 10^4)$ - $t$ is the number of test cases.

The first line of each test case contains a single integer $n$ where $(1 \leq n \leq 2 \cdot 10^5)$ - $n$ is the number
of containers with water.

The second line of each test case contains $n$ integers $a_1,a_2,...,a_n$ $0 \leq a_i \leq 10^9)$ - the amounts of water
in the containers. It is guaranteed that the sum of $a_i$ in each test case does not exceed $2 \cdot 10^9$. Also, the
sum of $a_i$ is divisible by $n$.

### Output
Output $t$ lines, each of which is the answer to the corresponding test case. As the answer, output `"YES"` if it is
possible to make the amount of water in all containers the same using the describe operation. Otherwise, output `"NO"`.

## Working through the problem

I struggled to complete a working solution initially. The key insight to solving this problem is summarised in one
sentence, and is even found in the problem statement - *The sum of $a_i$ is divisible by $n$*. What this sentence
indicates is that we can **calculate the average amount of water a bucket can hold and the average will be an integer.**. We can link this to whether we can determine  if it is possible to make the amount of water in all containers the same, 
