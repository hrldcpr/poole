---
layout: post
title: Continued Fraction Streams
mathjax: true
---

Every real number can be represented as a potentially infinite stream of digits and also as a potentially infinite continued fraction, for example:

$$
1.433127... = 1 + \frac{1}{2 + \frac{1}{3 + \frac{1}{4 + \frac{1}{5 + \ddots}}}}
$$

(The continued fraction can also be written using a less cool but probably more practical notation, as $$[1; 2, 3, 4, 5, \dots]$$)


## The goal

Suppose we're given a number as a stream of digits. Can we convert that to a stream of continued fraction coefficients?


## A problem

Let's say we've received the digits $$1.43...$$ from our input stream of digits. If we just truncate and compute the continued fraction[^1] we get:

$$
1.43 = [1; 2, 3, 14]
$$

So maybe we can safely emit these coefficients into our output stream and then keep emitting whatever coefficients come next?

$$
\begin{array}{r|l}
\text{digits in} & \text{coefficients out} \\
\hline
1 \\
. \\
4 \\
3 & 1 \\
& 2 \\
& 3 \\
& 14 \\
\vdots & \vdots
\end{array}
$$

Let's say we receive the digit $$3$$ next, so far giving us $$1.433...$$

$$
1.433 = [1; 2, 3, {\bf 4}, 3, 10]
$$

The first three coefficients are the same as what we've already outputted, but the fourth coefficient has changed from $$14$$ to $$4$$!

In most streaming situations there's no way to undo or change something we've already outputted, so we're doomed.


## A solution

We need to only emit a coefficient if we're sure it will never change.

We can do this by seeing what would happen if we receive the smallest or largest digits from the stream in the future, giving us bounds on what could possibly happen.

For example, if we've so far received $$1.43...$$ then the smallest number it could possibly be is if we receive *zeroes forever*, $$1.43000...$$, and the largest number it could be is if we receive *nines forever*, $$1.43999...$$ So we can bound the true number with:

$$
1.43000... \le 1.43... \le 1.43999...
$$

Of course, $$1.43000... = 1.43$$ (equivalent to the stream simply ending) and $$1.43999... = 1.44$$, both of which have finite continued fractions that we can compute[^1]:

$$
[{\bf 1}; {\bf 2}, {\bf 3}, 14] = 1.43 \le 1.43... \le 1.44 = [{\bf 1}; {\bf 2}, {\bf 3}, 1, 2]
$$

So no matter what the future digits in the stream are, we can be sure that $$[1; 2, 3, \dots]$$ will be the coefficients, and we can safely output them:

$$
\begin{array}{r|l}
1 \\
. \\
4 \\
3 & 1 \\
& 2 \\
& 3 \\
\vdots & \vdots
\end{array}
$$

If we go one digit at a time, we can output the first two coefficients even sooner, but happily it's the same stream:

$$
[1] = 1 \le 1.\dotso \le 2 = [2]
\\
[{\bf 1}; {\bf 2}, 2] = 1.4 \le 1.4... \le 1.5 = [{\bf 1}; {\bf 2}]
\\
[{\bf 1}; {\bf 2}, {\bf 3}, 14] = 1.43 \le 1.43... \le 1.44 = [{\bf 1}; {\bf 2}, {\bf 3}, 1, 2]
$$

$$
\begin{array}{r|l}
1 \\
. \\
4 & 1 \\
  & 2 \\
3 & 3 \\
\vdots & \vdots
\end{array}
$$



### Yeah!

Generalizing the previous example, we can always bound a stream of digits as:

$$
d_0.d_1d_2...d_n \le d_0.d_1d_2...d_n... \le d_0.d_1d_2...(d_n + 1)
$$


### In the other direction

Interestingly, we can bound continued fractions in almost exactly the same way as digits:

$$
(-1)^n [a_0; a_1, \dots, a_n] \le (-1)^n [a_0; a_1, \dots, a_n, \dots] \le (-1)^n [a_0; a_1, \dots, a_n + 1]
$$

Compare to the inequality for digits; they're almost identical.

The only difference is that the bounds alternate back-and-forth, because even coefficients increase the result, but odd coefficients decrease it.

Here are some example bounds to illustrate the general inequality above:

$$
1 \le 1 + \ddots \le 1 + \frac{1}{1}
\\
1 + \frac{1}{2} \ge 1 + \frac{1}{2 + \ddots} \ge 1 + \frac{1}{2 + \frac{1}{1}}
\\
1 + \frac{1}{2 + \frac{1}{3}} \le 1 + \frac{1}{2 + \frac{1}{3 + \ddots}} \le 1 + \frac{1}{2 + \frac{1}{3 + \frac{1}{1}}}
$$

(For these to make sense, keep in mind that coefficients are always positive integers.)

Now we can go from a stream of coefficients to a stream of digits, by converting these bounds to digits and using whatever parts are the same for both bounds:

$$
1 = [1] \le [1; \dots] \le [2] = 2
\\
{\bf 1}.5 = [1; 2] \ge [1; 2, \dots] \ge [1; 3] = {\bf 1}.333...
\\
{\bf 1}.{\bf 4}28571... = [1; 2, 3] \le [1; 2, 3, \dots] \le [1; 2, 4] = {\bf 1}.{\bf 4}44...
$$

$$
\begin{array}{r|l}
\text{coefficients in} & \text{digits out} \\
\hline
1 \\
2 & 1 \\
  & . \\
3 & 4 \\
\vdots & \vdots
\end{array}
$$


## The golden ratio

One of the countless interesting things about the golden ratio is that it's the "most irrational" number.

One way to glimpse this is by converting its stream of continued fraction coefficients (which is all ones!) to digits; it takes many coefficients to get each digit:

$$
\varphi = 1 + \frac{1}{\varphi} = 1 + \frac{1}{1 + \frac{1}{1 + \frac{1}{1 + \ddots}}} = 1.618033...
$$

$$
\begin{array}{r|l}
1 \\
1 & 1 \\
  & . \\
1 \\
1 & 6 \\
1 \\
1 \\
1 & 1 \\
1 \\
1 \\
1 \\
1 \\
1 & 8 \\
1 & 0 \\
1 & 3 \\
1 \\
1 \\
1 \\
1 \\
1 & 3 \\
\vdots & \vdots
\end{array}
$$

In fact, [Lochs' theorem](https://en.wikipedia.org/wiki/Lochs%27_theorem) says that for almost all numbers, each continued fraction coefficient determines 1.03 decimal digits. But for the golden ratio each coefficient only determines 0.42 decimal digits.

We can see both of these behaviors by comparing $$\varphi$$ to three random streams of digits:

![Number of coefficients versus number of decimals, for phi and three random numbers]({{ site.baseurl }}/assets/lochs-phi.svg)


[^1]:
    Here's how to compute the continued fraction $$1.43 = [1; 2, 3, 14]$$:

    $$
    \begin{array}{cc}
    x_0 = 1.43 = 1\frac{43}{100} & a_0 = 1 \\
    x_1 = \frac{100}{43} = 2\frac{14}{43} & a_1 = 2 \\
    x_2 = \frac{43}{14} = 3\frac{1}{14} & a_2 = 3 \\
    x_3 = \frac{14}{1} = 14 & a_3 = 14
    \end{array}
    $$
