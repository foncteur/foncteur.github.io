---
layout: post
description: This is a test
---


As this is the first post on this blog, it seems appropriate to present an *old* language (in the standards of computer science), that is still widely used.
In fact, it is not as much a language, as it is a *family* of programming languages. Lisp stands for "LISt Processing"; and it's all about, well... lists (we will see later that it is rather about pairs, but pairs are used to create lists, hence the name Lisp).
This is what makes it so powerful: a simple and yet very expressive language, that allows for much complexity by only operating on lists. Coding in Lisp feels like playing with a bunch of Legos! Inspired by Church's lambda calculus, it's a multi-paradigm language, but remains functional at its core.
I discovered it the year before my graduation, and I was struck by the elegance of it.

As I was saying, we are talking about a family of languages, so: how does one recognize a Lisp-like language?
In appearance, there is an easy way to tell: look out for parentheses. If you see a bunch of closing parentheses at the end of all functions, you are probably looking at Lisp code. There is no agreed-upon universal convention about what a Lisp precisely is. You will still see that the core of the language is roughly the same in various versions. In this post and a few others, I will present my own version of a minimalistic dialect, including a step-by-step explanation of how I built the interpreter for it.

Now, let's see a few examples, that should be enough to understand the general syntax, and start poking around!

------

Let's start with strings and ints.

First of all, the program `"hello world"`{.scheme} simply returns the string `"hello world"`{.scheme}. The result of the evaluation is then, as always, printed by the interpreter.
In this very simple language, we shall also have ints : `3`{.scheme} is a valid program, that will return `3`{.scheme}.

We also have a few primitives, such as `+`{.scheme}. For now, let's say that this operator applies to only two arguments.
It can be used as below:

```scheme
>>> (+ 17 42)
59
```
It is a prefix operator, unlike the addition in most modern languages. The use of parentheses is quite simple: it means that the first element is applied to the other elements.


We have booleans, represented by `#t`{.scheme} and `#f`{.scheme}, that evaluate to themselves. Another special symbol is `#nil`{.scheme} and will be useful for lists.

So, how do we manipulate lists? In fact, we rather use pairs. This means that, instead of using the list `(2 3 5)`{.scheme}, we have the following pair: `(2 (3 (5 #nil)))`{.scheme}. The last element is concatenated with the special symbol `#nil`{.scheme}, and then, this pair becomes the second part of a pair, and so on.

These two forms are strictly equivalent. However, as it is more convenient to work with lists, we shall have a constructor `list`{.scheme}. Applied to an arbitrary number of arguments, it will create the series of pairs representing the list of the arguments. Let's get back to pairs.

In order to manipulate pairs, we have to be able to:

- create a pair given its two elements,

- extract the first element of a given pair,

- extract the second element of a given pair.

We will then have three primitives that will do exactly this: `cons`{.scheme}, `car`{.scheme} and `cdr`{.scheme}.
Here is a few examples to show how these can be used.

```scheme
>>> #nil
#nil
>>> (cons 2 #nil)
(2, #nil)
>>> (cons 2 (cons 3 #nil))
(2, 3, #nil)
>>> (car (cons 2 3))
2
>>> (cdr (cons 2 3))
3
>>> (cdr (cons 2 (cons 3 #nil)))
(3, #nil)
```

We can see that, from a list point of view, `car`{.scheme} returns the first element of the list in argument, and `cdr`{.scheme} returns the list without its first element. We call this the *head* and *tail* of a list.


Another primitive which will be useful is `null?`{.scheme}, which returns `#t`{.scheme} if the list in argument is `#nil`{.scheme} and `#f`{.scheme} if it is not.

Other similar primitives available for ints are : `=`{.scheme}, `<=`{.scheme}, `>=`{.scheme}, `<`{.scheme} and `>`{.scheme}. These are prefix operators, and their meaning is self-explanatory.


Now we can talk about variables and bindings.

First, we can bind locally a variable. It corresponds to the `let a = 3 in expr`{.ocaml}. In order to achieve a local definition in our Lisp, we can use the keyword `let`{.scheme} as following:

```scheme
>>> (let x 2 (+ x x))
4
>>> x
Error: unbound variable
```

The primitive `let`{.scheme} takes three arguments: the variable name, the value we want to assign, and the expression we want to evaluate.

Another available primitive is `define`{.scheme}. It allows us to declare a global definition. This primitive takes two arguments: the name of the variable, and the value.
It doesn't return anything, and can be used as showed below:

```scheme
>>> (define x 2)
>>> (+ x x)
4
>>> (+ x (+ x x))
6
```

At this point, it seems only natural to introduce functions. We work with anonymous functions, as the keyword `define`{.scheme} can then be used to name them.
The keyword for that is `lambda`{.scheme}, in refence to Alonzo Church's lambda calculus. The `lambda`{.scheme} primitive takes two arguments: a list of arguments and the result expression. Let's look at some examples.

```scheme
>>> (lambda (x) (+ x x))
<fun>
```

Here, we have the function that takes an argument, named `x`{.scheme}, and returns the double of `x`{.scheme}. The variable `x`{.scheme} is between parentheses because the `lambda`{.scheme} primitive takes a *list* of arguments as its first argument.

This function is stricly equivalent to `(lambda (y) (+ y y))`{.scheme}, as we only renamed the variable.

If we want to sum two integers, we can write the following function:

```scheme
>>> (lambda (x y) (+ x y))
<fun>
>>> (define sum (lambda (x y) (+ x y)))
>>> (sum 2 3)
5
```

We can combine this with local definitions:

```scheme
>>> (let x 2 (lambda (x y) (+ x y)))
<fun>
```

This gives us the function that takes an int, and returns the sum of this int and 2.

Let's go back to the following example: `(define sum (lambda (x y) (+ x y)))`{.scheme}.
A shorter way to write that is `(define (sum x y) (+ x y))`{.scheme}. It is more easy to understand, but it only is syntactic sugar.

The last thing we need to start playing is the conditional instruction. It is rather straightforward:

```scheme
>>> (if (= 2 3) 1 5)
5
>>> (if (= 2 2) 1 5)
1
```

The primitive `if`{.scheme} takes, as you can see, three arguments. The first one will be evaluated to a boolean. If it is true, then the result of the whole expression will be the evaluation of the second argument. If not, then the third argument will be evaluated and returned.

Here we are, we have a basic description of this minimalistic language. As it is compatible with [guile](https://www.gnu.org/software/guile/), we will use this interpreter for now. This will allow us to learn how to use our Lisp. Then, I will show you how I wrote my own interpreter for this language.

-----

This is just intended to be an introduction to a very minimalistic language. In order to go further, I can only recommand ["Structure and Interpretation of Computer Programs"](https://en.wikipedia.org/wiki/Structure_and_Interpretation_of_Computer_Programs), by Abelson and Sussman. It's often considered as the "go-to book" about Lisp, interpreters, and more generally, an introduction to computer science.
