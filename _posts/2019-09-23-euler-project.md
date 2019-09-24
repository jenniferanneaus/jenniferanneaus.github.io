---
layout: post
current: post
cover:  assets/images/euler.jpg
navigation: True
title: The Euler Project
date: 2019-09-18 12:00:00
tags: [python, maths]
class: post-template
subclass: 'post tag-python tag-maths'
---

When I was searching for coding challenges recently, I came across [Project Euler](https://projecteuler.net/). This website has problems that range in difficulty, but generally involve a mixture of skills in mathematics and coding. I decided to try a few problems in Python, which turned out to be a lot of fun.

I started with [Problem 6](https://projecteuler.net/problem=6), a simple problem. It asks us to do the following:

> Find the difference between the sum of the squares of the first one hundred natural numbers and the square of the sum.

That is, we want to calculate $(1+2+...+100)^2-(1^2+2^2+...+100^2)$. Given that 100 is small in computer terms, this problem could easily be solved using a ```for``` loop. However, summing $n$ and $n^2$ reminded me of the maths I was teaching not so long ago. I decided to use the formulae for these sums to make my solution efficient, and easily able to be scaled up to numbers much larger than 100. The two formulae[^1] are as follows:

+ $\displaystyle\sum_1^n n = \frac{n(n+1)}{2}$
+ $\displaystyle\sum_1^n n^2 = \frac{n(n+1)(2n+1)}{6}$

The first formula, for summation of the first $n$ natural numbers, is famously attributed to Gauss. It is said he was asked to calculate $1+2+...+100$ as a young schoolboy; though his teacher expected the problem to keep him occupied for a long time, Gauss cleverly noticed a pattern and solved the problem quite quickly. Generalising using the case $1+2+...+n$, we can visualise it as follows. First, we put the numbers 1 to $n$ in columns.
![Sum of integers 1](assets/images/sum_natural_numbers_1.png)
Then, we take another copy of the numbers 1 to $n$. Put this copy in reverse order, and place it below the first.
![Sum of integers 2](assets/images/sum_natural_numbers_2.png)
Now each column adds up to a total of $n+1$.
![Sum of integers 3](assets/images/sum_natural_numbers_3.png)
Therefore, we have a total of $n(n+1)$. However, since we added two copies of each integer instead of just one, we need to halve our answer to obtain $\displaystyle\sum_1^n n = \frac{n(n+1)}{2}$. Pretty cool, huh?

Using the mathematical formulae in the code for our solution, we have two functions to write. To calculate $\displaystyle\sum_1^n n$ we have:

```python
def sum(n):
  ans = (n*(n + 1)) / 2
  return ans
```

Then, to calculate $\displaystyle\sum_1^n n^2$ we have:

```python
def squares_sum(n):
  ans = (n*(n + 1)*(2*n + 1)) / 6
  return ans
```

Calculating the final solution is then simply a matter of squaring the result of the first function, and subtracting the result of the second. Thus we end up with

```python
def square_sums_difference(n):
  ans = sum(n) ** 2 - squares_sum(n)
  return ans
```

and so Problem 6 of the Euler Project is complete.

The next problem I looked at was [Problem 1](https://projecteuler.net/problem=1), which can also make use of our ```sum(n)``` function. A statement of the problem is below.

> Find the sum of all the multiples of 3 or 5 below 1000.



[comment]: you can link to another blog post with [Sum of Squares post]({% post_url 2019-09-23-sum-of-squares %})


[^1]: Note the use of sigma notation in sums: 
    + $\displaystyle\sum_1^n n$ is equivalent to $1+2+...+n$, and 
    + $\displaystyle\sum_1^n n^2$ is equivalent to $1^2+2^2+...+n^2$.