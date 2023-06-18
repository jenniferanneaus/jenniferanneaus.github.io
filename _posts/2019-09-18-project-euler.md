---
layout: post
current: post
cover: assets/images/covers/euler.jpg
navigation: True
title: Project Euler
date: 2019-09-18 12:00:00
# tags: python maths
class: post-template
subclass: "post tag-python tag-maths"
---

When I was searching for coding challenges recently, I came across [Project Euler](https://projecteuler.net/). This website has problems that range in difficulty, but generally involve a mixture of skills in mathematics and coding. I decided to try a few problems in Python, which turned out to be a lot of fun.

I started with Problem 6; to skip ahead to my discussion of other problems you can click [here for Problem 1](#problem-1) or [here for Problem 46](#problem-46).

[Problem 6](https://projecteuler.net/problem=6) is a simple question, which asks:

> Find the difference between the sum of the squares of the first one hundred natural numbers and the square of the sum.

That is, we want to calculate $(1+2+...+100)^2-(1^2+2^2+...+100^2)$. Given that 100 is small in computer terms, this problem could easily be solved using a `for` loop. However, calculating sums for $n$ and $n^2$ reminded me of the maths I was teaching not so long ago. <a name="return-from-footnote"></a> I decided to use the formulae for these sums to make my solution efficient, and easily able to be scaled up to numbers much larger than 100. The two formulae[$^1$](#footnote) are as follows:

- $\displaystyle\sum_1^n n = \frac{n(n+1)}{2}$
- $\displaystyle\sum_1^n n^2 = \frac{n(n+1)(2n+1)}{6}$

The first formula, for summation of the first $n$ natural numbers, is famously attributed to Gauss. It is said he was asked to calculate $1+2+...+100$ as a young schoolboy; though his teacher expected the problem to keep him occupied for a long time, Gauss cleverly noticed a pattern and solved the problem quite quickly. Using the sum $1+2+...+n$, we can visualise it as follows. First, we put the numbers 1 to $n$ in columns.
![Sum of integers 1](assets/images/posts/sum_natural_numbers_1.png)
Then, we take another copy of the numbers 1 to $n$. Put this copy in reverse order, and place it below the first.
![Sum of integers 2](assets/images/posts/sum_natural_numbers_2.png)
Now each column adds up to a total of $n+1$.
![Sum of integers 3](assets/images/posts/sum_natural_numbers_3.png)
Therefore, we have a total of $n(n+1)$. However, since we added two copies of each integer instead of just one, we need to halve our answer to obtain $\displaystyle\sum_1^n n = \frac{n(n+1)}{2}$. Pretty cool, huh?

Getting back to our code, we have two functions to write. To calculate $\displaystyle\sum_1^n n$ we have:

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

<a name="problem-1"></a>
and so Problem 6 of the Euler Project is complete.

The next problem I looked at was [Problem 1](https://projecteuler.net/problem=1), which can also make use of our `sum(n)` function. A statement of the problem is below.

> Find the sum of all the multiples of 3 or 5 below 1000.

To solve this problem, we need to make two observations:

- The sum $3+6+...+999$ is the same as the sum $3(1+2+...+333)$.
- If we add each multiple of 3 and then each multiple of 5, we will have counted each multiple of 15 twice.

Using the first observation, we see that we can find the sum of all multiples of $n$ up to and including 999 by evaluating the sum $1+2+...+(999/n)$ and multiplying it by $n$. This gives us the following function:

```python
def sum_one_factor(n, maximum):
	occurences = maximum / n
	ans = n * sum(occurences)
	return ans
```

Turning our attention to the second observation, we can find the multiples of 3 or 5 by adding the multiples of 3 to the multiples of 5, then subtracting the multiples of 15 to compensate for having double counted them:

```python
def sum_two_factors(fact_one, fact_two, maximum):
	ans = sum_one_factor(fact_one, maximum) + sum_one_factor(fact_two, maximum)\
	- sum_one_factor(fact_one*fact_two, maximum)
	return ans
```

<a name="problem-46"></a>
All we need to remember is that, given the problem specifies _below_ 1000 and our functions include the maximum in their sum, we should call `sum_two_factors(3,5,999)` to get the correct answer.

The final problem I coded for this post is [Problem 46](https://projecteuler.net/problem=46), titled "Goldbach's Other Conjecture". The goal of this problem is to find the first counterexample to the following statement:

> Every odd composite number can be written as the sum of a prime and twice a square.

Recall that a composite number is an integer greater than 1 that is not prime. We will begin our solution by writing a function to check whether a number is prime. Python doesn't have an inbuilt function for this; although libraries with prime checkers exist, it is simple enough for us to write our own function. Intuitively, we need to know whether the given number $n$ has any factors between $2$ and $n-1$. However, notice that if $n$ has a factor greater than or equal to $\sqrt{n}$, it must also have a factor less than or equal to $\sqrt{n}$. Therefore, we only need to check for factors between 2 and $\sqrt{n}$:

```python
def is_prime(n):
	for i in range(2, int(math.sqrt(n)+1)):
		if n%i == 0:
			return False
	return True
```

Note that we have added 1 to `math.sqrt(n)` because `range()` excludes the upper bound.

Since the question relates to odd numbers and all primes are odd except for 2, we will treat 2 as a special case and add it manually to a list called `primes`. We then find the solution by iterating through odd numbers, starting with 3, and either adding them to our `primes` list or checking whether it satisfies the conjecture. The code for this is as follows:

```python
def disprove_goldbachs_other_conjecture():
	found = False
	current = 3
	primes = [2]
	while not found:
		if is_prime(current):
			primes.append(current)
		else:
			if create_sum(current, primes) == 0:
				return current
		current = current + 2
```

What remains is to write a function `create_sum(n, primes)` to check Goldbach's conjecture. We know that if a number $n$ is to be written as the sum of a prime and twice a square, then the prime must be smaller than $n$. In our case, the list `primes` already contains the potential candidates, so we will pass that to the function. We then check each prime $p$ in the list to see whether the difference between $n$ and $p$ is 2 times a perfect square. To do that, we can use the following:

```python
def create_sum(n, primes):
	for p in primes:
		if math.floor(math.sqrt((n-p)/2)) == math.sqrt((n-p)/2):
			return p
	return 0
```

So there we have it, problem solved! There are hundreds of questions on the Project Euler website, each with a different balance of coding skills and mathematical know-how. My next blog post is about [Sum of Squares]({% post_url 2019-09-21-sum-of-squares %}), another problem from Project Euler that turned out to be mathematically challenging and worthy of a separate post. To see the Python files, visit [my GitHub repository](https://github.com/jenniferanneaus/project_euler_code).

<a name="footnote"></a>
<br>

<sub><sup> $1.$ Note the use of sigma notation in sums: </sup></sub>
<br><sub><sup>• $\displaystyle\sum_1^n n$ is equivalent to $1+2+...+n$, and </sup></sub>
<br><sub><sup>• $\displaystyle\sum_1^n n^2$ is equivalent to $1^2+2^2+...+n^2$. [↩](#return-from-footnote)</sup></sub>
