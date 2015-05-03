---
layout: post
title: A basic introduction to einsum
---

I haunt the `[numpy]` tag on Stack Overflow. After watching the steady trickle of incoming questions, it's become apparent that a lot of askers find the `einsum` function somewhat puzzling.

Often they've read the (informative but terse) documentation and are unsure how to begin to apply the function. Sometimes they're trying to use it on a problem that can only really be solved with good old-fashioned broadcasting and array methods. Either way, it's an indication that simple explanations and introductory guides can be difficult to find online.

`einsum` itself is one of NumPy's jewels and can frequently lead to significant increases in speed and efficiency. It does take a little while to get used to the notation and, once understood, sometimes a few attempts to apply it correctly to a problem. This post is a basic introduction to what this function does and how it can be used to replace more familiar array operations.

## What `einsum` does

Suppose you have two arrays, `A` and `B`. Now suppose that you want to...

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

It's not limited to two arrays either. The function can operation can be performed on multiple operations in one go.



## How to use `einsum`

To use `einsum` we need to label axes of the arrays we're going to operate on. 

The function lets us do that in one of two ways: using a string of letters, or using lists of integers. For simplicity, this post will stick to the first option (which appears to be the more commonly-used of the two).

Take the matrix multiplication example from the documentation. For two 2D arrays `A` and `B`, matrix multiplication can be done with `np.einsum('ij,jk->ik', A, B)`.

What does this string mean? Well, think of `'ij,jk->ik'` split in two at the arrow `->`. The first part labels the axes of the *input* arrays: `'ij'` labels `A` and `'jk'` labels `B`. The second part of the string labels the axes of the single *output* array with the letters `'ik'`. In other words, we're putting two 2D arrays in and getting a new 2D array out.

Here's a picture to show what's going on. The two arrays I'll use are:

``` python
>>> A = np.array([[1, 1, 1],[2, 2, 2],[5, 5, 5]])
>>> B = np.array([[0, 1, 0],[1, 1, 0],[1, 1, 1]])
```

Then drawing on the labels our matrix multiplication `np.einsum('ij,jk->ik', A, B)` looks like this:

<img src="{{ site.baseurl }}/images/matrix_mul_reduce.png" "colour-pairs" style="width: 500px;"/>

To understand how the output array is calculated, remember these three rules:

- **Repeating letters between input arrays means that values along those axes will be multiplied together. The products make up the values for the output array.**

In this case, we used the letter `j` twice: once for `A` and once for `B`. This means that we're multiplying each row of `A` with each column of `B`. Obviously this will only work if the axis labelled by `j` is the same length in both arrays (or the length is 1 in either array).

- **Omitting a letter from the output means that values along that axis will be summed.**

Here, `j` is not included among the labels for the output array. Leaving it out sums along the axis and explicitly reduces the number of dimensions in the final array by 1. Had the output signature been `'ijk'` we would have ended up with a 3x3x3 array of products. (And if we gave *no* output labels but just write the arrow, we'd simply sum the whole array.)

- **We can return the unsummed axes in any order we like.**

If we leave out the arrow `'->'`, NumPy will take the labels that appeared once and arrange them in alphabetical order (so in fact `'ij,jk->ik'` is equivalent to just `'ij,jk'`). If we want to control what our output looked like we can choose the order of the output labels ourself. For example, `'ij,jk->ki'` delivers the transpose of the matrix multiplication (notice that `k` and `i` were switched in the output labelling). 

It should now be easier to see how the matrix multiplication worked. This image shows what we'd get if we *didn't* sum the `j` axis and instead included it in the output by writing `np.einsum('ij,jk->ijk', A, B)`). To the right, axis `j` has been summed:

<img src="{{ site.baseurl }}/images/matrix_mul_full_and_reduce.png" "colour-pairs" style="width: 400px;"/>


## A glossary of simple operations

That's really all you need to start using `einsum`. Knowing how to multiply different axes together and then how to sum the products, we can express a lot of different operations succinctly, especially when a lot of dimensions are involved. Because `einsum` allows broadcasting and doesn't build arrays with unnecessary dimensions when we want to sum over an axis, it can be more memory-efficient.

Below are two tables showing how `einsum` can stand in for various NumPy operations. It's useful to play about with these to get the hang of the notation.

Let `A` and `B` be two 1D arrays of compatible shapes (i.e. one axis can be broadcast to the other):

| **`einsum` call signature**           | **NumPy equivalent** | **Comments**                |
| ---------------------------- | ---------------------- | --------------------- |
| `('i', A)`             | `A`                      | returns a view of `A`|
| `('i->', A)`           | `sum(A)`              | sums the values of `A`  |
| `('i,i->i', A, B)`      | `A * B`                | element mult. of `A` and `B`|
| `('i,i', A, B)`        | `inner(A, B)` | inner product of `A` and `B` |
| `('i,j', A, B)`    | `outer(A, B)`  | outer product of `A` and `B` |


Now let `A` and `B` be two 2D arrays with compatible shapes:

| **`einsum` call signature**   | **NumPy equivalent** | **Comments** |
| ---------------------------- | ------------------- | ---------------------- |
| `('ij', A)`            | `A`                     | returns a view of `A`|
| `('ji', A)`            | `A.T`                   |view transpose of `A` |
| `('ii->i', A)`            | `diag(A)`          | view main diagonal of `A`|
| `('ii', A)`            | `trace(A)`                   | sums main diagonal of `A`  |
| `('ij->', A)`          | `sum(A)`             | sums the values of `A` |
| `('ij->j', A)`          | `sum(A, axis=0)`    | sums the columns of `A` |
| `('ij->i', A)`          | `sum(A, axis=1)`    | sums the rows of `A` |
| `('ij,ij->ij', A, B)`  | `A * B`    | element mult. of `A` and `B` |
| `('ij,ji->ij', A, B)` | `A * B.T`   | element mult. of `A` and `B.T` |
| `('ij,jk', A, B)`  | `dot(A, B)`    | matrix mult. of `A` and `B`|
| `('ij,jk->ij', A, B)` | `inner(A, B)` | inner product of `A` and `B` |
| `('ij,jk->ijk', A, B)` | `A[:,None]*B` | each row of `A` multiplied by `B` |  
| `('ij,kl->ijkl', A, B)` | `A[:,:,None,None]*B` | each value of `A` multiplied by `B` |

If you're familiar with these results, it's possible to start applying the ideas to arrays with more dimensions. Just make sure that the axes you want to multiply together are compatible, and drop any labels of axes you want to sum.
 
## A few quirks to watch out for

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

Finally, `einsum` is not always the fastest option. Functions such as `dot` and `inner` often link to lightening-quick BLAS routines which can outperform `einsum` and certainly shouldn't be forgotten about. The `tensordot` function is also worth comparing for speed. If you search around, you'll find examples of posts highlighting cases where `einsum` appears to be slow, especially when operating on several arrays (like [this GitHub issue](https://github.com/numpy/numpy/issues/5366)).
