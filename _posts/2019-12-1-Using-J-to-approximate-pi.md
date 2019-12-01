---
layout: post
title: Using J to approximate pi
---

<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      processEscapes: true
    }
  });
</script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

You might see this:

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">An interesting approximation of π that is accurate up to four digits. 🤓 <a href="https://t.co/FscKjpX3X5">pic.twitter.com/FscKjpX3X5</a></p>&mdash; Fermat&#39;s Library (@fermatslibrary) <a href="https://twitter.com/fermatslibrary/status/1188440971563937792?ref_src=twsrc%5Etfw">October 27, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

and think:

> Cool! But how accurate is that?
>
> Let me use my calculator/favourite programming language to check...

You proceed to check, but:

> ..._ugh_, this feels like more effort that necessary.
>
> There should be a quicker way to write this loop or combine these functions.

Situations like this are compelling reasons for learning a language that allows you to express or extend computional patterns with minimal effort.

Many languages provide a lot of power for a minimal number of keystrokes, but I want to focus on the [J programming language](https://www.jsoftware.com/#/) and a few of its numerous clever constructs that are not-too-difficult to learn and which make swathes of equations quick to type and verify.

Instead of tediously typing out variables function definititons and looping over the numbers, you can compose just three or four J primitives to do the job. J has an incredibly rich set of functions and a grammar that permits multiple modes of composition.

Before you read on, I should tell you that I myself am very much at the beginner stage in J. This should:

1. reassure you that the language is useful to those who are far from an intermediate/advanced level
2. alert you to the fact that my explanations in this post may not be as nuanced or informative as those from a more experienced user

Regarding the second point, I've taken care to try and explain concepts as clearly as I can, but if something does not make sense it's likely to be because I've missed some subtlety or worded things clumsily. If that is the case, please do refer to an actual expert: I've linked to several resources at the foot of the page. (And please leave me a comment so I can improve the post!)

To introduce and explore the tools of J, let's continue the theme of approximating $\pi$ using formulae picked from [Wikipedia](https://en.wikipedia.org/wiki/Approximations_of_%CF%80).

## Simple J arithmetic

Here's a well-known approximation which is accurate to two decimal places:

$$ \frac{22}{7} \approx \pi $$

Like many other languages, `+`, `-` and `*` represent addition, subtraction and multiplication in J. However, the symbol `%` is the _verb_ for division:

```
    22 % 7
3.14286
```

Note that in the J interpretor, the line that is executed is indented, followed by the unindented result.

There are a couple of things to say here. Firstly, the word "verb" is used instead of "function". Verbs can either be _monadic_ (take a single argument on the right-hand side) or _dyadic_ (take two arguments, one on either side). Above, the dyadic form of `%` was used. The monadic form of `%` gives the reciprocal, e.g. `% 2` is 0.5.

Secondly, verbs in J associate to the right and have the same precedence as each other.

For instance, consider:

$$ \sqrt{2} + \sqrt{3} $$

Knowing that the monadic verb `%:` computes the square-root of its argument, we could try:

```
   %: 2 + %: 3
1.93185
```

But this is wrong. Because of the right-associativity and equal precedence of these verbs, we are actually computing $ \sqrt{ 2 + \sqrt{3} }$ here. For the correct result, we could use paretheses as in many other programming languages:

```
   (%: 2) + (%: 3)
3.14626
```

Here we compute the square-roots of 2 and 3, then added these results using the dyadic verb `+`. There are arguably better ways to write this equation though.

## Array building and reduction

J is an array-based language. Its verbs can operate not just on single values, but on arrays containing multiple values. Elements in arrays are separated by a single space:

```
   2 3
2 3
```

To compute the square-root of each element of the array, apply the verb to the array:

```
    %: 2 3
1.41421 1.73205
```

How can we sum the elements of this new array? To do this we can modify the verb `+` using the _insert_ adverb `[/](https://code.jsoftware.com/wiki/Vocabulary/slash)`. This has the effect of putting the verb between each element of the array. For example, `+/ 7 8 9` is equivalent to `7 + 8 + 9`.

For our purposes, `+/` precedes `%:` to sum the array of square roots:

```
   +/%: 2 3
3.14626
```

You can see how this array-based approach implies looping: we're essentially applying functions to multiple values but there's no need for explicit `for (i; i < length, i++)` syntax.

Let's take the concept of applying multiple verbs to arrays a step further.

The tweet at the top of this post featured the formula:

$$ ln(6) ^ ln(5) ^ ... ^ ln(2)  \approx \pi $$

One way to write this power-tower of logarithms in J is:

```
    ^/ ^. |. 2 + i.5
3.14158
```
Here is how this expression works. Let's first pick out the numbers 2, 3, 4, 5 and 6.

```
    i.5
0 1 2 3 4

    2 + i.5
2 3 4 5 6
```
The verb `i.` gives you an array of integers from 0 up to the specified argument. In this case, we want five integers. We also want to begin at 2 rather than 0, so we add 2 to each of the numbers in the array using `+`.

Looking at the original equation and reading up the tower, it will be convenient to consider these integers numbers in reverse order. The monadic verb `|.` reverses the array:

```
    |. 2 + i. 5
6 5 4 3 2
```

Next, we require the natural logarithm of each integer. To produce an array of these logarithms, we apply the verb `[^.](https://code.jsoftware.com/wiki/Vocabulary/hatdot)`:

```
    ^. |. 2 + i. 5
1.79176 1.60944 1.38629 1.09861 0.693147
```

Finally, we want to stack these logarithms in a tower and evaluate the result. In J, the verb `[^](https://code.jsoftware.com/wiki/Vocabulary/hat)` is used to raise the left-hand argument to the power of the right-hand argument. J also knows that this verb is right-associative, so `x ^ y ^ z` will be treated as $x ^ {y ^ z}}$.

We want to stick `^` between each element of our array. We do this by using the adverb `[/](https://code.jsoftware.com/wiki/Vocabulary/slash)` to modify `^` and produce the new verb `^/` to operate on the array:

```
    ^/ ^. |. 2 + i. 5
3.14158
```

## Hooks

werwerewrwer


## Forks

Consider another approximation of $\pi$:

$$ \sqrt{\frac{9}{5}} + \frac{9}{5} $$

We can translate this as:
```
    (%: 9 % 5) + 9 % 5
3.14164
```
This works, but it feels unsatisfactory for a couple of reasons:

- repetition of `9 % 5`: we could abstract this away
- use of parentheses to force precedence

Looking more closely at the repetition of the fraction, we take its square root before adding the result back to itself.

This can be represented in J using a monadic _fork_. A fork in J is a sequence of three verbs.

$$ (f g h) x $$

$$ (f x) g (h x) $$


using a fork:
```
    (%: + ]) 9 % 5
3.14164
```




