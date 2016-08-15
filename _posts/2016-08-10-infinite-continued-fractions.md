---
layout: post
title: Infinite Continued Fractions
mathjax: true
---

Every real number can be represented as a potentially infinite stream of digits and also as a potentially infinite continued fraction, for example:

$$
1.433127\ldots = 1 + \frac{1}{2 + \frac{1}{3 + \frac{1}{4 + \frac{1}{5 + \dots}}}}
$$

The continued fraction can also be written using a less cool but probably more practical notation, as $$[1;2,3,4,5,\dots]$$


## The Goal

Suppose we're given a number as a stream of digits, and want to convert that to its stream of continued fraction coefficients. Is that possible?

And how about the other direction, going from the stream of coefficients to the stream of digits?


## The Problem

### Digits to Coefficients

Let's say we've received the digits $$1.43\dots$$ from our input stream of digits. If we just truncate and convert to a continued fraction we get:

$$
1.43 = [1;2,3,14]
$$

So maybe we can safely emit these coefficients into our output stream and then keep emitting whatever coefficients come next?

Next we receive the digit $$3$$, so far giving us $$1.433\dots$$ Then:

$$
1.433 = [1;2,3,{\bf 4},3,10]
$$

The first three coefficients are the same as what we've already outputted, but the fourth coefficient has changed to $$4$$!

There's typically no way to undo or change something we've already outputted, so we're doomed.

### Coefficients to Digits

The same problem happens in the other direction, from coefficients to digits. For example:

$$
[1;2,3] = 1.4{\bf 2}8571428571428571\dots
\\
[1;2,3,4] = 1.4{\bf 3}3333333333333333\dots
$$

(In fact it's even worse since a finite continued fraction can lead to an infinite stream of digits.)


## A Solution

### Digits to Coefficients

So we need to only emit a coefficient if we're sure it will never change.

We can do this by seeing what would happen if we received the smallest or largest possible digits from the stream in the future, giving us bounds on what could possibly happen.

For example, if we've so far received $$1.43\dots$$ then the smallest number it could possibly be is if we receive zeroes forever, $$1.43000\dots$$, and the largest number it could possibly be is if we receive nines forever, $$1.43999\dots$$ So we can bound the true number with:

$$
1.43000\ldots \le 1.43\ldots \le 1.43999\dots
$$

But note that $$1.43000\ldots = 1.43$$ and $$1.43999\ldots = 1.44$$, both of which have finite continued fractions that we can compute:

$$
[{\bf 1};{\bf 2},{\bf 3},14] = 1.43 \le 1.43\ldots \le 1.44 = [{\bf 1};{\bf 2},{\bf 3},1,2]
$$

So no matter what the future digits in the stream are, we can be sure that $$[1;2,3,\dots]$$ will be the coefficients, and we can safely output them.

### Coefficients to Digits

We can do the same thing in the other direction, from coefficients to digits.

For example, if we've so far received $$[1;2,\dots]$$ then since all coefficients are positive, any further coefficients will lead to a smaller number, so we can bound above by truncation:

$$
[1;2,\dots] = 1 + \frac{1}{2 + \dots} \le 1 + \frac{1}{2} = [1;2]
$$

And since all coefficients are positive integers, the smallest possible coefficient is $$1$$, so we can bound below by the next coefficient being $$1$$:

$$
[1;2,1] = 1 + \frac{1}{2 + \frac{1}{1}} \le 1 + \frac{1}{2 + \dots} = [1;2,\dots]
$$

So

$$
{\bf 1}.333\dots = [1;2,1] \le [1;2,\dots] \le [1;2] = {\bf 1}.5
$$

$$
1 + \frac{1}{2 + \frac{1}{1 + x}} \le 1 + \frac{1}{2 + \frac{1}{a + x}} \le 1 + \frac{1}{2}
$$


So, we can always bound a stream of digits as:

$$
d_0.d_1d_2 \dots d_n \le d_0.d_1d_2 \dots d_n \ldots \le d_0.d_1d_2 \dots (d_n + 1)
$$

And we can always bound a stream of continued fractions as:

$$
(-1)^n [a_0; a_1, \dots, a_n] \le (-1)^n [a_0; a_1, \dots, a_n, \dots] \le (-1)^n [a_0; a_1, \dots, a_n + 1]
$$
