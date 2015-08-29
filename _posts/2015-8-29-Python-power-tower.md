---
layout: post
title: Python power towers
---

If you have a list of numbers, e.g. `[3, 2, 2, 2]` what's the best way to turn it into a power tower?

In other words, the aim is to take the list and calculate the answer to `3**(2**(2**2))`, which is 43046721 (the `**` operator is exponentiation in Python).

Trivial... or so I thought for ten seconds.  

It turns out that this is a bit of a pain to get right. Unlike addition and multiplication, exponentiation is not commutative. That is, `x**y` does not necessarily equal `y**x`. This means you can't simply reduce the list using `pow` (Python's built in function for exponentiation) because you'll get 6561, that is `((3**2)**2)**2`). Power towers are collapsed from the top; the list must be reduced 'backwards'.

For a first attempt at turning a list into a power tower, recall that Python is smart enough to evaluate a sequence of powers from right to left:

```
>>> 3**2**2**2
43046721
``` 

Therefore, if `lst` is your list of numbers `[3, 2, 2, 2]`, you *could* write:

```
>>> eval('**'.join([str(x) for x in lst])
43046721
```

But this is unspeakably ugly: (a) it turns each number into a string and constructs the expression, and (b) it uses the much-maligned `eval` to execute the string expression (using the safer function  `ast.literal_eval` won't work here).

There has to be a better way! After some pencil-on-paper scribbling, the nicest I could come up with was this.

```python
from functools import reduce # not necessary in Python 2.x

def pow_swap_args(x, y):
    return y ** x
```

Then we can write:

```
>>> reduce(pow_swap_args, reversed([3, 2, 2, 2]))
43046721
``` 

This is a definite improvement over using `eval`. Reversing the list takes care of the reduce right-to-left requirement, a power function with swapped arguments completes the calculation.

I'm still sure there's another nicer way. Ultimately I want to find either a nicer way to swap the arguments of `pow`, or a way to order the list that avoids the need to create a new function. If I discover this elusive better way, I'll post an update here.
