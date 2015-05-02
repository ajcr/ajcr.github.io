---
layout: post
title: A basic guide to NumPy's `einsum`
---

I haunt the `[numpy]` tag on Stack Overflow. After watching the steady trickle of incoming questions, it's become apparent that a lot of askers find the `einsum` function somewhat puzzling.

Often they've read the (informative but terse) documentation and are unsure how to begin to apply the function. Sometimes they're trying to use it on a problem that can only really be solved with good old-fashioned broadcasting and array methods. Either way, it's an indication that simple explantions and introductory guides can be difficult to find online.

`einsum` itself is one of NumPy's jewels and can frequently lead to significant increases in speed and efficiency. It does take a little while to get used to the notation and, once understood, sometimes a few attempts to apply it correctly to a problem. This post is a basic introduction to what this function does and how it can be used to replace more familiar array operations.



### What `einsum` does

Suppose you have two arrays, `A` and `B`. Now suppose that you want to

- **multiply** `A` with `B` in a particular way to create new array of products, *and then maybe*
- **sum** this new array along particular axes, *and/or*
- **transpose** the axes of the array in a particular order.

There's a very good chance `einsum` will help you do that much faster and more memory-efficiently that combinations of the NumPy functions `multiply`, `sum` and `transpose` will allow.

As a small example of `einsum`'s power, here are two arrays that we want to multiply element-wise and then sum along axis 1 (the rows):

``` python
A = np.array([0, 1, 2])

B = np.array([[ 0,  1,  2,  3],
              [ 4,  5,  6,  7],
              [ 8,  9, 10, 11]])
              
```
How do we normally do this in NumPy? The first thing to notice is that we need to reshape `A` so that we can broadcast it with `B` (specifically `A` needs to be column vector). Then we can multiply `0` with the first row of `B`, multiply `1` with the second row, and `2` with the third row. This will give use a new array and the three rows can then be summed.

Putting this together, we have:

``` python
>>> (A[:, np.newaxis] * B).sum(axis=0)
array([ 0, 22, 76])
```

This works fine, but, using `einsum`, we can do better:

``` python
>>> np.einsum('i,ij->i', A, B)
array([ 0, 22, 76])
```

Even for this tiny example, `einsum` is about three times faster. Why? We didn't need to reshape `A` and, most importantly, the multiplication didn't create a temporary array like `A[:, np.newaxis] * B` did. Instead, `einsum` simply summed the products along the rows as it went and returned them in an array.

It's not limited to two arrays either. The function can operation can be performed on mutliple operations in one go.



### How to use `einsum`

To use `einsum` we need to label axes of the arrays we're going to operate on. 

The function lets us do that in one of two ways: using a string of letters, or using lists of integers. For simplictiy, this post will stick to the first option (which appears to be the more commonly-used of the two).

Take the matrix multiplication example from the documentation. For two 2D arrays `A` and `B`, matrix multiplication can be denoted with the string of letters `'ij,jk->ik'`:

``` python
np.einsum('ij,jk->ik', A, B)
```

What does this mean? Think of the string `'ij,jk->ik'` split in two at the arrow `->`. The first part labels the axes of the *input* arrays: `'ij'` labels `A` and `'jk'` labels `B`. The second part of the string labels the axes of the single *output* array with the letters `'ik'`. In other words, we're putting two 2D arrays in and getting a new 2D array out.

Here's a picture to show what's going on. The two arrays I'll use are:

``` python
>>> A = np.array([[1, 1, 1],[2, 2, 2],[5, 5, 5]])
>>> B = np.array([[0, 1, 0],[1, 1, 0],[1, 1, 1]])
```

Then drawing on the labels we have:

<img src="{{ site.baseurl }}/images/matrix_mul_reduce.png" "colour-pairs" style="width: 800px;"/>

To understand how the output array is calculated, remember these three rules:

**(1) Repeating letters between input arrays means that values along those axes will be multiplied together. The products make up the values of the output array.**

In this case, we used the letter `j` twice: once for `A` and once for `B`. This means that we're multiplying the rows of `A` with the columns of `B`. Obviously this will only work if the axis labelled by `j` is the same length in both arrays (or the length is 1 in either array). This is element-wise multiplication.

**(2) Ommiting a letter from the output means that values along that axis will be summed.**

Here, `j` is not included in output labelling. Had the output signature been `'ijk'` we would have ended up with a 3x3x3 array of products. Leaving it out sums along the axis and explicitly reduces the number of dimensions in the final array by 1. (And if we gave *no* output labels and just write `->` we'd simply sum the whole array.)

**(3) We can return the unsummed axes in any order we like.**

If we leave out the arrow `'->'`, NumPy will take the labels that appeared once and arrange them in alphabetical order (so in fact `'ij,jk->ik'` is equivalent to just `'ij,jk'`). If we wanted to control what our output looked we can choose the order of the output labels ourself. For example, `'ij,jk->ki'` delivers the transpose of the matrix multiplication (notice that `k` and `i` were switched in the output labelling). 

It should now be easier to see how the matrix multiplication worked. The image below shows what we'd get if we *didn't* sum the `j` axis and instead included it in the output, writing `np.einsum('ij,jk->ijk', A, B)`). To the right, axis `j` has been summed:


<img src="{{ site.baseurl }}/images/matrix_mul_full_and_reduce.png" "colour-pairs" style="width: 800px;"/>


### A glossary of simple operations

That's really all you need to start using `einsum`. Knowing how to multiply different axes together and then how to sum the products, we can express a lot of different operations succinctly, especially when a lot of dimensions are involved. Because `einsum` allows broadcasting and doesn't build arrays with unecessary dimensions when we want to sum over an axis, it can be more memory-efficient.

Below are two tables showing how `einsum` can stand in for various NumPy operations. It's useful to play about with these to get the hang of the notation.

Let `A` and `B` be two 1D arrays of compatible shapes (i.e. one axis can be broadcast to the other):
 
| `einsum` operation           | Plain NumPy equivalent | Comments                |
| :------------------          | :----------------        | :------               |
| `einsum('i', A)`             | `A`                      | returns a view of `A`|
| `einsum('i->', A)`           | `np.sum(A)`              | sum the values of `A`  |
| `einsum('i,i->i', A, B)`      | `A * B`                |element-wise multiplication of `A` and `B`|
| `einsum('i,i', A, B)`        | `np.inner(A, B)` **or** `np.dot(A, B)` **or** `(A * B).sum()` |inner product of `A` and `B`|
| `einsum('i,j', A, B)`    | `np.outer(A, B)` **or** `A[:, None] * B` | outer product. `'i,j->ji'` transposes the outer product|


Now let `A` and `B` be two 2D arrays with compatible shapes:

| `einsum` operation           | Plain NumPy equivalent | Comments                |
| :------------------          | :----------------    | :------               |
| `einsum('ij', A)`            | `A`                     | returns a view of `A`|
| `einsum('ji', A)`            | `A.T`                   |view of the transpose of `A` |
| `einsum('ii->i', A)`            | `np.diag(A)`          | view the main diagonal of `A` (by repeating a label for an array) |
| `einsum('ii', A)`            | `np.trace(A)`                   | sum the main diagonal of `A`  |
| `einsum('ij->', A)`          | `np.sum(A)`             |sum all the values of `A` |
| `einsum('ij->j', A)`          | `np.sum(A, axis=0)`    |sum the columns of `A` |
| `einsum('ij->i', A)`          | `np.sum(A, axis=1)`    |sum the rows of `A` |
| `einsum('ij,ij->ij', A, B)`  | `A * B`    | element-wise multiplication of `A` and `B` |
| `einsum('ij,ji->ij', A, B)` | `A * B.T`   | element-wise multiplication of `A` and `B.T` |
| `einsum('ij,jk', A, B)`  | `np.dot(A, B)`    | matrix multiplication of `A` and `B`. To return the transpose, append '`->ki'` |
| `einsum('ij,jk->ij', A, B)` | `np.inner(A, B)` | inner product of `A` and `B` (sum product over the last axes) |
| `einsum('ij,jk->ijk', A, B)` | `A[:, None] * B` |     broadcasting: 3D array, each row of `A` multiplied by `B` |  
| `einsum('ij,kl->ijkl', A, B)` | `A[:, :, None, None] * B` | broadcasting: 4D array, each value of `A` multiplied by `B` |

As you begin to use more dimensions, it's also possible to use ellipses `...` to denote axes of an aray.


 
### Quirks to watch out for

Here a few things to be mindful of when using the function.

`einsum` [does not promote datatypes when summing](http://stackoverflow.com/a/18366008/3923281). If you're using a more limited datatype, you might get unexpected results: 

``` python
>>> a = np.ones(300, dtype=np.int8)
>>> np.sum(a) # correct result
300
>>> np.einsum('i->', a) # incorrect result
44
```

`einsum` [might not permute axes in the intended order](http://stackoverflow.com/a/28233465/3923281). The documentation highlights `np.einsum('ji', M)` as a way to transpose a 2D array. You'd be forgiven for thinking that for a 3D array, `np.einsum('kij', M)` moves the last axis to the first position and shifts the first two axes along. Actually, `einsum` creates its own output labelling by rearranging labels in alphabetical order. Therefore `'kij'` becomes `'kij->ijk'` and we have a sort of inverse permutation instead.

Finally, `einsum` is not always the fastest option. Functions such as `dot` and `inner` often link to lightening-quick BLAS routines which can outperform `einsum` and certainly shouldn't be forgotten about. The `tensordot` function is also worth comparing for speed. If you search around, you'll find examples of 
