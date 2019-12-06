---
layout: post
title: J hooks make exotic expressions easy
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

You might see the approximation:

$$ \ln(6) ^ {\ln(5) ^ {\ln(4) ^ {\ln(3) ^{\ln(2)}}}} \approx \pi $$

and think:

__Hmmm! How accurate is that?__

You might then proceed to check using a scientific calculator, your phone, or your favourite programming language. But if you're like me, your next thought will be:

__...this is tedious and surely more effort than necessary for a simple pattern.__

Here we have a compelling reason to learn a language that allows us to express and extend computional patterns with minimal effort.

I want to focus on the [J programming language](https://www.jsoftware.com/#/) and one of its many clever abstractions: hooks. This is not-too-difficult to learn and will make swathes of repetitive mathematical expressions typable in a dozen or so characters. Once you get the hang of the language, it's a great replacement for a scientific calculator.

You can even get a free [J interpretor app](https://apps.apple.com/us/app/j-programming-language/id532587550) for your phone.

Before you read on, I should tell you that I myself am very much a beginner in J. This fact should:

1. reassure you that the language is useful to those who are well below an intermediate/advanced level
2. alert you to the fact that my explanations in this post may not be as nuanced or informative as those from a more experienced user

Regarding (2) I've taken care to try and explain the key points clearly, but for a more complete overview please do refer to an actual expert! I've linked to several resources at the foot of the page.

Now to introduce and explore a few of the useful tools of J, let's continue the theme of approximating $\pi$ using expressions picked from [Wikipedia](https://en.wikipedia.org/wiki/Approximations_of_%CF%80) and [Wolfram Mathworld](http://mathworld.wolfram.com/PiApproximations.html).

## J arithmetic

Here's a well-known approximation which is accurate to two decimal places:

$$ \frac{22}{7} \approx \pi $$

Like many other languages, `+`, `-` and `*` represent addition, subtraction and multiplication of two numbers in J. However, the symbol [`%`](https://code.jsoftware.com/wiki/Vocabulary/percent#dyadic) is the _verb_ for division:

```j
    22 % 7
3.14286
```

Note that in the J console, the line that is executed is indented, followed by the (unindented) result.

There are a couple of things to say here. Firstly, the word "verb" is used instead of "function" in J. Verbs are (usually) _monadic_ (take a single argument on the right-hand side) or _dyadic_ (take two arguments, one on either side). Above, the dyadic form of `%` was used. The monadic form of `%` gives the reciprocal, e.g. `% 2` is 0.5.

Secondly, verbs in J associate to the right and have the same precedence as each other. For instance, consider:

$$ \sqrt{2} + \sqrt{3} \approx \pi $$

Knowing that the monadic verb [`%:`](https://code.jsoftware.com/wiki/Vocabulary/percentco) computes the square-root of its argument, we could try:

```j
   %: 2 + %: 3
1.93185
```

Because of the right-associativity and equal precedence of these verbs, we are actually computing $ \sqrt{ 2 + \sqrt{3} }$. To get the correct result, one option is to use parentheses to force the evaluation in the order we require:

```j
   (%: 2) + (%: 3)
3.14626
```

Here we compute the square-roots of 2 and 3, then add these results using the dyadic verb `+`. However, we can write expressions such as these more easily and idiomatically using J's fundamentental data structure: the array.

## Array building and reduction

J is an array-based language. Its verbs can operate not just on single values, but on arrays (i.e. sequences of one or more values in one or more dimensions). Syntactically, elements in one-dimensional arrays are separated by a single space. For instance, an array containing the integers 2 and 3 is written:

```j
   2 3
```

To compute the square root of each element of the array, apply the verb to the array:

```j
    %: 2 3
1.41421 1.73205
```

How can we sum the elements of this new array? To do this we can modify the verb `+` using the _insert_ adverb [`/`](https://code.jsoftware.com/wiki/Vocabulary/slash). This has the effect of putting the verb between each element of the array. For example, `+/ 7 8 9` is equivalent to `7 + 8 + 9`.

For our purposes, `+/` is placed to the left of `%:` to sum our array of square roots:

```j
   +/ %: 2 3
3.14626
```

You can see how this array-based approach implies looping. We're essentially applying functions to multiple values but there's no need for explicit `for (i; i < length, i++)` looping syntax.

To carry the pattern of applying multiple functions to arrays a step or two further, let's look at the interesting expression:

$$ \ln(6) ^ {\ln(5) ^ {\ln(4) ^ {\ln(3) ^{\ln(2)}}}} \approx \pi $$

One easy way to evaluate this power-tower of logarithms in J is:

```j
    ^/ ^. |. 2 + i.5
3.14158
```

Here is how it works. Let's first pick out the numbers 2, 3, 4, 5 and 6 which appear in the expression:

```j
    i.5
0 1 2 3 4

    2 + i.5
2 3 4 5 6
```
The monadic verb [`i.`](https://code.jsoftware.com/wiki/Vocabulary/idot) gives you an array of integers from 0 up to the specified argument. In this case, we want five integers. We also want to begin at 2 rather than 0, so we add 2 to each of the numbers in the array using `+`.

Reading up the tower, it will be convenient to consider these integers numbers in reverse order. The monad [`|.`](https://code.jsoftware.com/wiki/Vocabulary/bardot) reverses the array:

```j
    |. 2 + i. 5
6 5 4 3 2
```

Next, we require the natural logarithm of each integer. To produce an array of these logarithms, we apply the mondaic verb [`^.`](https://code.jsoftware.com/wiki/Vocabulary/hatdot):

```j
    ^. |. 2 + i. 5
1.79176 1.60944 1.38629 1.09861 0.693147
```

Finally, we want to stack these logarithms in a tower and evaluate the result. In J, the dyadic verb [`^`](https://code.jsoftware.com/wiki/Vocabulary/hat) is used to raise the left-hand argument to the power of the right-hand argument. As we have seen, J expressions are right-associative so `x ^ y ^ z` will be treated as $x ^ {y ^ z}$.

We want to stick `^` between each element of our array. We do this by using the adverb `/` again to modify `^` and produce the new verb `^/` to operate on the array:

```j
    ^/ ^. |. 2 + i. 5
3.14158
```

As a final example of arrays and reduction, consider the Gregory–Leibniz series:

$$ 4 \sum^\inf_{n=0} \frac{(-1)^n}{2n+1} = 4 (\frac{1}{1} - \frac{1}{3} + \frac{1}{5} -+ ...) = \pi $$

Look at the denominators: if we put those in an array, find the reciprocal values and compute the alternative sum then we're done.

J can't handle limits, so we'll only use the first 1 million odd integers. If you'll allow me to deviate from the central point of this post for one moment, one nice consequence of J's grammar is that we can assign names to arrays and verbs and then the resulting expression is just like reading English:

```j
    odd_integers =: 1 + 2 * i. 1000000
    alternating_sum_of =: -/
    reciprocals_of =: %

    4 * alternating_sum_of reciprocals_of odd_integers
3.14159
```

## Hooks

So far we have executed verbs on arrays one after the other in the sequence they appear from right to left.

For example, `-%: 4` will produce $-2$, as both the verbs `-` (negative) and `%:` (square root) operate in turn on their input, equivalent to the expression $-\sqrt{4}$. 

J allows multiple verbs grouped with parentheses to be composed using [trains](https://www.jsoftware.com/help/learning/09.htm). A train consisting of two verbs is called a hook. This is an interesting form of function composition that allows a single input to be operated on separately by two different functions to produce a new result.

To motivate hooks, here's another approximation of $\pi$, due to Ramanujan:

$$ \frac{9}{5} + \sqrt{\frac{9}{5}} \approx \pi $$

We could translate into J this as:
```j
    (9%5) + %: 9%5
3.14164
```

But this is needlessly verbose: repeating the single numerical value and using parentheses for the correct precedence is not necessary in J. Instead, we'll use a hook consisting of these two verbs:

```j
    (+%:) 9%5
3.14164
```
This computes the square root of the input and then adds this new value back on to the input.

If there is a left-hand argument to the hook, then it uses that argument instead of the first argument (a dyadic hook).

It turns out that dyadic hooks are great for repetive mathematical expressions:

$$ \sqrt{ 7 + \sqrt{ 6 + \sqrt{ 5 } } } \approx \pi $$

There's an obvious pattern we should take advantage of here. We have three integers $7$, $6$ and $5$ with an operation (square-root and then add) between each one, to be evaluated from the right-hand side to the left.

In other words we want to insert (using `/`) the dyadic hook `(+ %:)` between these integers:

```j
    %: (+%:)/ 7 6 5
3.14163
```

The final (left-most) square-root completes the expression. No need for any explicit nesting of functions, temporary variables or recursion that may be necessary in other languages.

Imagine the tedium of trying to verify this approximation using a scientific calculator, or even a more mainstream programming language:

$$ 768{\sqrt {2-{\sqrt {2+{\sqrt {2+{\sqrt {2+{\sqrt {2+{\sqrt {2+{\sqrt {2+{\sqrt {2+{\sqrt {2+1}}}}}}}}}}}}}}}}}} \approx \pi $$ 

Hmmm, so that's a run of seven $2$'s, then $2+1$, all preceded by a couple of other operations. Knowing that the dyad [`$`](https://code.jsoftware.com/wiki/Vocabulary/dollar#dyadic) will build an array of the right-hand side value repeated left-hand-side-many times, and the dyad [`,`](https://code.jsoftware.com/wiki/Vocabulary/comma#dyadic) concatenates arrays, we can write this in J without much effort:

```j
    768 * %: 2 - %: (+%:)/ (7$2),2+1
3.14159
```

As we've made it this far, here's one final use of hooks: continued fractions.

$$ \pi = 3+{\cfrac {1^{2}}{6+{\cfrac {3^{2}}{6+{\cfrac {5^{2}}{6+\ddots }}}}}} $$

This continued fraction is merely an 'add-six-then-divide' pattern so our hook is just `(% 6&+)` (the [`&`](https://code.jsoftware.com/wiki/Vocabulary/ampm) binds an argument to a verb, making a dyad into a monad). The only slightly fiddly bit is building an array of odd squares. I'll use `*: 1 + 2 * i.100` to build an array of the first hundred, so then:

```j
    3 + (% 6&+)/ *:1+2*i.100
3.14159
```

## More J

That was a quickfire introduction to simple arithmetic, one-dimensional arrays, a few J verbs, and finally hooks.

This barely scratches the surface of J programming, but I think it makes a convincing case for J as a means to evaluating repetitive mathematical expressions that are tedious to type in many other languages.

To go beyond this post, there are excellent J resources available online:

- First and foremost the [J Software website](https://code.jsoftware.com/wiki/Main_Page). I particularly like the [essays](https://code.jsoftware.com/wiki/Essays) which focus on solving specific mathematical and algorithmic problems.

- The creator of J, [Kenneth Iverson](https://en.wikipedia.org/wiki/Kenneth_E._Iverson), wrote many books and articles about the language. I read '[Arithmetic](https://www.jsoftware.com/books/pdf/arithmetic.pdf)' first and found it both incredibly helpful and the style very readable.

- Henry Rich's '[J for C programmers](https://www.jsoftware.com/help/jforc/contents.htm)' is a great resource and helped me understand J's concept of 'rank' a little bit better.

- Unsuprisingly, the thoughts and themes in this post are not unique. Half-way through typing it I searched and found various other blog posts. Here are a couple I read: '[Handwriting J](https://www.hillelwayne.com/post/handwriting-j/)', '[Beyond Functional Programming: Manipulate Functions with the J Language](https://www.adamtornhill.com/articles/jlang/beyondfunctional.html)'.
