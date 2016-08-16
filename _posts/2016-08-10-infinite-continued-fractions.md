---
layout: post
title: Infinite Continued Fractions
mathjax: true
---

Every real number can be represented as a potentially infinite stream of digits and also as a potentially infinite continued fraction, for example:

$$
1.433127... = 1 + \frac{1}{2 + \frac{1}{3 + \frac{1}{4 + \frac{1}{5 + \ddots}}}}
$$

The continued fraction can also be written using a less cool but probably more practical notation, as $$[1;2,3,4,5,\dots]$$


## The goal

Suppose we're given a number as a stream of digits, and want to convert that to its stream of continued fraction coefficients. Is that possible?


## A problem

Let's say we've received the digits $$1.43...$$ from our input stream of digits. If we just truncate and convert to a continued fraction we get:

$$
1.43 = [1;2,3,14]
$$

So maybe we can safely emit these coefficients into our output stream and then keep emitting whatever coefficients come next?

Next we receive the digit $$3$$, so far giving us $$1.433...$$ Then:

$$
1.433 = [1;2,3,{\bf 4},3,10]
$$

The first three coefficients are the same as what we've already outputted, but the fourth coefficient has changed to $$4$$!

There's typically no way to undo or change something we've already outputted, so we're doomed.


## A solution

We need to only emit a coefficient if we're sure it will never change.

We can do this by seeing what would happen if we receive the smallest or largest possible digits from the stream in the future, giving us bounds on what could possibly happen.

For example, if we've so far received $$1.43...$$ then the smallest number it could possibly be is if we receive zeroes forever, $$1.43000...$$, and the largest number it could possibly be is if we receive nines forever, $$1.43999...$$ So we can bound the true number with:

$$
1.43000... \le 1.43... \le 1.43999...
$$

But note that $$1.43000... = 1.43$$ and $$1.43999... = 1.44$$, both of which have finite continued fractions that we can compute:

$$
[{\bf 1};{\bf 2},{\bf 3},14] = 1.43 \le 1.43... \le 1.44 = [{\bf 1};{\bf 2},{\bf 3},1,2]
$$

So no matter what the future digits in the stream are, we can be sure that $$[1;2,3,\dots]$$ will be the coefficients, and we can safely output them.


## Yeah

Generalizing the previous example, we can always bound a stream of digits as:

$$
d_0.d_1d_2...d_n \le d_0.d_1d_2...d_n... \le d_0.d_1d_2...(d_n + 1)
$$

## Going in the other direction

Interestingly, you can bound continued fractions in almost exactly the same way as digits:

$$
(-1)^n [a_0; a_1, \dots, a_n] \le (-1)^n [a_0; a_1, \dots, a_n, \dots] \le (-1)^n [a_0; a_1, \dots, a_n + 1]
$$

The only difference is that the bounds alternate back-and-forth, because even coefficients increase the result, but odd coefficients decrease it.

Here are some example bounds to illustrate the general inequality above:

$$
1 \le 1 + \ddots \le 1 + 1
\\
1 + \frac{1}{2} \ge 1 + \frac{1}{2 + \ddots} \ge 1 + \frac{1}{2 + 1}
\\
1 + \frac{1}{2 + \frac{1}{3}} \le 1 + \frac{1}{2 + \frac{1}{3 + \ddots}} \le 1 + \frac{1}{2 + \frac{1}{3 + 1}}
$$

(For these to make sense, keep in mind that coefficients are always positive integers.)
