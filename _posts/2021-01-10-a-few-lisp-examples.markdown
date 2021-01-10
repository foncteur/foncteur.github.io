---
layout: post
description: A few examples of functions written in Lisp.
---

Now that I have introduced Lisp in the previous (and first) post, let's play
with it a little.
I will use the [guile](https://www.gnu.org/software/guile/) syntax, so feel free
to test the examples on your machine and implement some more!

As Lisp is designed to handle lists, I used the [OCaml List standard library](https://caml.inria.fr/pub/docs/manual-ocaml/libref/List.html) as an inspiration.
Below are the first few functions, implemented in Lisp.

-----
First, let's code `map`. It takes a function and a list as arguments, and returns
the list of all the elements of the input list, to which the function has been applied.

```scheme
>>> (define (map f l) (if (null? l) #nil (cons (f (car l)) (map f (cdr l)))))
<fun>
```

Let's define a list; it will be useful for tests.

```scheme
>>> (define l (list 1 2 3 4))
```

Now let's test our `map` function.

```scheme
>>> (display (map (lambda (x) (* x x)) l))
(1 4 9 16)
```

So, as expected, the interpreter prints (with the `display` function) the list of
the first four squares.

Let's print a new line:
```scheme
>>> (display #\newline)

```
and while we're at it, let's define a more simple function that simply prints a new line.
```scheme
>>> (define (newline) (display #\newline))
<fun>
```

Now, here are a few functions for computing the length of a list, and comparing lengths.

```scheme
>>> (define (length l) (if (null? l) 0 (+ 1 (length (cdr l)))))
<fun>
>>> (display (length l))
4
>>> (newline)

>>> (define (compare-lengths l1 l2)
      (if (null? l1)
        (if (null? l2) 0 (-1))
        (if (null? l2) 1 (compare-lengths (cdr l1) (cdr l2)))))
<fun>
>>> (define l2 (list 5 6))
>>> (display (compare-lengths l l2))
1
>>> (newline)

>>> (define (compare-length-with l n)
      (if (null? l)
        (if (equal? 0 n) 0 (if (< 0 n) -1 1))
        (compare-length-with (cdr l) (- n 1))))
<fun>
>>> (display (compare-length-with l 2))
1
(newline)
```

I hope that thanks to these few examples, using Lisp will appear simpler.
In particular, the general structure (recursive functions, which begin by testing
if the list is empty, and call themselves if not) should now be clearer.
