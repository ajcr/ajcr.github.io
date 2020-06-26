---
layout: post
title: The fast way to count permutations with no repeated letters
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

There are 12 unique permutations of the word 'food'. Of these, there are only six that satisfy the constraint that no adjacent letters are equal:

- fodo
- odof
- odfo
- ofdo
- ofod
- dofo

How can we efficiently *count* the number of such permutations for a given word?

## Approach 1: Brute Force

The simplest approach is to generate each and every possible permutation of the word in turn, checking whether any adjacent letters are equal, and incrementing the count if not.

The following code uses Python's itertools module to generate the permutations (and also to simplify the process of checking for repeated letters):

```python
from itertools import permutations, groupby, filterfalse

def length(iterable):
    """
    Length of an iterable (but not necessarily Sized) object.
    """
    return sum(1 for _ in iterable)

def has_equal_adjacent_letters(string):
    """
    Indicate whether string has adjacent letters which are equal:

      toot -> True
      yoyo -> False

    """
    return any(length(letters) > 1 for _, letters in groupby(string))

def approach_1(string):
    """
    Count the permutations of string that have no repeated letters
    by generating all permutations and checking them one by one.
    """
    wanted_perms = filterfalse(has_equal_adjacent_letters, permutations(string))
    # keep track of unique permutations using a set
    unique_perms = set(wanted_perms)
    return len(unique_perms)
```

For the word 'food' this works nicely:

```python
>>> approach_1('food')
6
```

However, we're in trouble if our word is much longer. As you may be aware, the number of (not necessarily unique) permutations of a word of length $n$ is

$$n! = n \cdot (n-1) \cdot (n-2) \cdot \ldots \cdot 2 \cdot 1$$

If our word is 'television' then we have to wait about 30 seconds to get our answer because the code generates all $10! = 3628800$ permutations and checks each of them before returning the answer:

```python
>>> approach_1('television')
584640
```

For 11-letter words, this time will increase roughly 11-fold to about 330 seconds, for 12-letter words... you get the idea.

We could possibly optimise the Python code a bit, but the naive brute force approach is doomed to be slow.

## Approach 2: Backtracking

To try and speed things up, we could try and be more careful about how permutations are generated.

The problem with the last approach is that it there was no way to skip all permutations that had a bad beginning. For example, it had to generate and check every permutation of 'television' that started with 'ee' and 'ii' even though none of these added to the count.

A common way to implement this more careful approach is to use [backtracking](https://en.wikipedia.org/wiki/Backtracking). Here, the idea is to construct "good" permutations of the word one letter at a time, stopping if we cannot add any letter that violates our constraint and resuming from the previous state.

Backtracking naturally suggests recursion, and this is the paradigm the code below uses:

```python
from collections import Counter

def next_letters(word, counts):
    """
    Filter the keys in the counts dictionary to find
    the letters that do not match the last letter in
    the current permutation.
    """
    if word:
        return [char for char in counts if char != word[-1]]
    else:
        return list(counts)

def extend_word(counts, word, target_len, total):
    """
    If the current word is of the target length n,
    increment the total and return.

    Else, recursively extend the word one letter at
    a time, then backtrack.
    """
    if len(word) == target_len:
        return total + 1

    for letter in next_letters(word, counts):

        counts -= Counter(letter)
        word.append(letter)

        total = extend_word(counts, word, target_len, total)

        counts += Counter(letter)
        word.pop()

    return total

def approach_2(string):
    """
    Count the permutations of a string that have no
    repeated letters by adding one letter at a time,
    backtracking when the permeation is full or no
    next letter can legally be added.
    """
    counts = Counter(string)
    target_len = len(string)
    return extend_word(counts, [], target_len, 0)
```

Let's test this code:

```python
>>> approach_2('food')
6
>>> approach_2('television')
584640
```

Same answers as before. It works correctly!

This algorithm is significantly faster than the brute force approach for words with a lot of repeated letters. However, this code will sometimes execute more slowly than the brute force approach, especially if there are mostly unique letters in the string ('television' took around 50 seconds compared to about 30 seconds previously).

The reason is that we're doing some very high-level manipulation of Python objects when tracking these permutations. Thousands and millions of attribute lookups and instance checks have a real impact on performance.

The speed can no doubt be improved in various ways (fewer "high-level" manipulations of Python objects, a non-recursive implementation), but we will shirk this task and try a third approach instead.

## Approach 3: SymPy and Generalised Laguerre Polynomials

Let's step back a moment and recall that our goal here was to simply *count*, rather than generate, the permutations. We know that constructing permutations one after the other can be slow, so it stands to reason that a more mathematical approach to derive the count will be quicker.

The place to start is to go through the string and count the frequency of each letter. If you enjoy combinatorics, you may decide to play around and use these frequencies to find some optimisations that will cut down the run time of the code. For instance, we could treat the two 'o' in 'food' as a single letter and count $3!$ illegal arrangements to the deduce that there must be $12 - 3! = 6$ good ones.

If you know a little bit about [generating functions](https://en.wikipedia.org/wiki/Generating_function), you might get even further and find an explicit formula for specific cases. Still, to me, this particular problem seems fundamentally difficult to solve in full generality using a strictly mathematical approach.

In 2012 I read [Jair Taylor's solution](https://math.stackexchange.com/a/129802/172714) to this permutation problem on math.stackexchange.com in which he presents a remarkable formula. It turns out that the count of permutations in which no two adjacent letters are equal is given by:

$$\int_0^\infty e^{-x} \prod_i q_{k_i}(x) \, dx$$

where $q_k(x) = \sum_{i=1}^k \frac{(-1)^{i-k}}{i!} {k-1 \choose i-1}x^i$ when $k \geq 1$ and $q_0(x) = 1$.

The way to use this approach is as follows:

1. Find the count $k_i$ of each letter in the word.
2. For each count, generate the polynomial $q_{k_i}(x)$.
3. Multiply the polynomials together, multiply by $e^{-x}$ and evaluate the integral.

Note that the integral is actually simple to solve, as $\int_0^\infty e^{-x} x^n \, dx$ can be evaluated as $n!$ (see the [Gamma function](https://en.wikipedia.org/wiki/Gamma_function)).

The polynomials used here are a form of [generalised Laguerre polynomials](https://en.wikipedia.org/wiki/Laguerre_polynomials#Generalized_Laguerre_polynomials) which, it turns out, have a generating function that is helpful to counting words with no repeated letters. The full details is beyond the scope of this blog post, but Taylor notes in his answer that the formula is known in the literature and points to a [paper by Ira Gessel](http://people.brandeis.edu/~gessel/homepage/papers/rookp.pdf) which offers some elucidation of this and similar techniques. The best explanation I've found so far is the one by [Nick Shales on Quora](https://www.quora.com/In-how-many-cases-there-will-be-no-two-people-of-the-same-nationality-sitting-next-to-each-other).

It any case, this approach can be implemented using a symbolic mathematics library such as SymPy:

```python
from collections import Counter

import sympy
from sympy import assoc_laguerre
from sympy.abc import x

# N.B. assoc_laguerre creates a generalised Laguerre polynomial

def eval_gamma(term):
    """
    Evaluate the term k*x**n as k*n!
    """
    coeff, exp = term.as_coeff_exponent(x)
    return coeff * sympy.factorial(exp)

def approach_3(string):
    """
    Count the permutations of string that have no repeated letters
    by multiplying Laguerre polynomials with degrees determined by
    the frequency of each letter and evaluating this product as
    a definite integral.
    """
    letters = Counter(string)
    # multiply polynomials given by the frequency of each letter
    product = sympy.prod(assoc_laguerre(degree, -1, x) for degree in letters.values()).apart()
    terms = product.as_ordered_terms()
    # evaluate \sum \integral_0^oo term*e^-x dx
    return abs(sum(eval_gamma(t) for t in terms))
```

We have used fewer lines of code than in the backtracking approach! Now let's see how it performs:

```python
>>> approach_3('food')
6
>>> approach_3('television')
584640
```

We see the same answers and the two previous approaches, but counting the permutations for 'television' took just 0.006 seconds. Using this awesome new firepower we can fight far more fearsome words.

## Challenge: OEIS A190945

The sequence [A190945](http://oeis.org/A190945) on OEIS counts the permutations of the string with one 'a', two of letter 'b', three of letter 'c', etc., such that adjacent letters are not equal:

```
a               = 1
abb             = 1
abbccc          = 10
abbcccdddd      = 1074
abbcccddddeeeee = 1637124
...
```
Currently, the sequence shown there stops at the string containing 11 occurrences of letter 'k'. There are

```
1562137388 4080024363 9670502529 6003247844
7581634808 28800
```

such permutations of this string.

Taking aim at the string 'abbccc...zzzzzzzzzzzzzzzzzzzzzzzzzz' (that's 26 occurrences of 'z'), we can count the following number of permutations with no adjacent letters equal to each other in just 10 seconds:

```
1997511428 0340904092 0511371088 5094277089
3326429735 7642982266 9965857719 2824672783
4680652395 8742393273 0713003359 0497965664
0025300168 2542458560 9834355399 2156036962
8635929100 9769477390 5097373040 7473565155
2042000124 8446825674 9869348683 1636948894
2357694312 4210362447 3563500920 0067009063
9251880581 0397811974 9862297861 7764758530
0383241026 3057476041 0628880518 1627576218
0635063343 8842149054 2821275327 2061603971
3945575172 4477888877 0427817831 9892480000
0
```

That's 441 digits. Needless to say, there is no point trying to compute this value using brute force or backtracking.
