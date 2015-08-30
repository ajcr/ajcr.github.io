---
layout: post
title: Python power towers
---

If you have a list of numbers, say `[3, 2, 2, 2]`, what's the best way to turn it into a power tower?

In other words, the aim is to take the list and calculate the answer to `3**(2**(2**2))`, which is 43046721 (the `**` operator is exponentiation in Python).

Trivial... or so I thought.  

It turns out that this is a bit of a pain to do nicely. Unlike addition and multiplication, exponentiation is not commutative. That is, `x**y` does not necessarily equal `y**x`. This means you can't simply reduce the list using `pow` (Python's built in function for exponentiation) because you'll get 6561, that is `((3**2)**2)**2`. Power towers are collapsed from the top; the list must somehow be reduced 'backwards' or 'from the inside-out'.

For a first attempt at turning a list into a power tower, recall that Python is smart enough to evaluate an expression with chained powers from right to left:

```
>>> 3**2**2**2
43046721
``` 

Therefore, if `x` is your list of numbers `[3, 2, 2, 2]`, you *could* create the same expression by writing:

```
>>> eval('**'.join([str(n) for n in x]))
43046721
```

But this is unspeakably ugly. It turns each number into a string, builds the expression string and then finally uses the much-maligned `eval` to calculate the answer (using the safer function `ast.literal_eval` won't work here). I never want to look at it again.

There has to be a better way! After some pencil-on-paper scribbling, I arrived at this:

```python
from functools import reduce # not necessary in Python 2.x

def pow_swapped_args(x, y):
    return y**x
```

Then we can evaluate a list as a power tower by writing:

```
>>> reduce(pow_swapped_args, reversed([3, 2, 2, 2]))
43046721
``` 

This is a definite improvement over the previous method. Reversing the list takes care of the reduce right-to-left evaluation requirement; a power function with swapped arguments completes the calculation.

I'm still sure there's another nicer way. I want to find either a different way to swap the arguments of `pow`, or a way to order the list that avoids the need to create a new function. Ultimately I'd like to find a fast vectorised way to do this on NumPy arrays instead of lists. If I discover any improved ways to make power towers, I'll post an update here.
