---
layout: post
title: "Function Properties"
date: 2019-03-17 10:00
comments: true
published: true
summary: In functional programming, abstractions tend to be very generic and talk about
properties of things
tags:
    - scala
    - haskell
    - learning
    - functional
    - fp tutorial
---

<span class="prev">[← Previous in this series: What is FP? - Part 2](/articles/What-is-FP-Part2/)</span>

In the previous chapter, we've gone into the differences between expressions
and statements and different definitions of functions.
Now, we're going to talk about properties of functions.

Unlike the two previous ones, this chapter will not introduce a lot of new concepts,
instead it will use the knowledge from the previous ones to help us define what some
function properties mean.

In functional programming, abstractions tend to be very generic and talk about
properties of things, but the abstractions themselves tend to be very
specific, in the sense that they usually follow well defined laws and the
properties it abstract over are very well defined.

The function abstraction is not conceptually different in FP than it is in
other paradigms, but properties of functions are at the very core of FP, and
awareness of those properties heavily shape FP code.

# Functions

The following are two properties of functions. It's very important to keep
in mind that they're not judgements of value about those functions, they're
just straight cold properties that tell us how those functions behave.

## Purity

Purity is a property of functions, and as properties come, this one is quite
binary, because a function is either pure of effecful. 
A functions is said to be _pure_ when it doesn't cause observable changes
outside of itself, and doesn't depend on the world outside of itself. In practice,
this means that its output and computations happening inside of it depend
exclusively on its input values. This correlates closely to our description of
expressions in the previous chapter, although it doesn't necessarily mean that
there are no statements inside of the function, as long as those statements
act only in at a local scope, as for example a local auxiliary assignment
statement to set a local variable, or a conditional statement (might be worth
noting here that both in Scala and Haskell `if/else` is an expression, not a
statement like in most other languages). 

By opposition, an _effectul_ function is one that causes an observable change in
the world or depends on the state of the world. This mean that functions that
do printing, writing to a file, writing to a database, publishing to Kafka,
read from a console, query a database, read from a file, get the current date,
etc... are all _effectul_. Now remember how in the last chapter I said that
`println` was a statement? I was a bit creative with the truth in there. In
some languages `print(ln)` is a statement, in some others it's an effecful
function that returns a `void` type (not always named `void` depending on the
language), which is an uninhabited type, meaning there are no values of type
`void`, and so the function returns nothing.
In Python, for example, `print` is a _statement_, in version 2, and became a
function in version 3:

```python
$ python2

>>> a = print "hello"
  File "<stdin>", line 1
    a = print "hello"
            ^
SyntaxError: invalid syntax
>>> print "hello"
hello
>>> exit()

$ python3

>>> a = print("hello")
hello
>>> print(a)
None
```

But in Scala `println` doesn't implement any of the previous behaviours. So,
what does it do then? In Scala, `println` is a function, but it doesn't return
a `void` type, instead it returns a value of type `Unit`. `Unit`, is a type
that is inhabited by a single value, `()` in Scala. Returning `Unit`, is usually
a pretty good hint that this functions is being executed purely for the effects
that it performs and we don't care about the result. This difference between a
`void` and `Unit` type is important because by being able to construct a value
of type `Unit`, which you can't for type `void` (because there are no values
of type `void`), means your function behaves similarly to all of your other
functions and actually returns a value you can do things with.

In functional programming, developers usually try to segregate _pure_
functions and _effectul_ functions, and have specific ways to deal with
functions that are _effectul_.

# Totality

Totality is usually less regarded that purity, but it's still an important
property of functions that people tend to care about in FP. A function is said
to be _total_ when every set of input values maps to an output value. It is
said to be _partial_ [^1] if any of the possible input combinations doesn't map to
a value (again this should ring a bell back to what we said about expression
properties in the previous chapter).
In practice this means that the function always returns something whatever is
the parameter you pass to it. An example of a total function could be:

```scala
def compare(x: Int, y: Int): Boolean = x == y
```

Because whatever pair of integers you pass to it, it should be able to return
the result of comparing those integers.

An example of a _partial_ function could be the following:

```scala
def div(x: Float, y: Float): Float = x / y
```

If you pass `0` as the `y` parameter it will throw a
`java.lang.ArithmeticException: / by zero`, meaning it doesn't return a
`Float` value, as expected. This also means that this function performed an
effect (throwing an exception). The `div` function is _partial_ because it
doesn't have a return value for any input combination where `y` is `0`.

Knowing about this property is useful, because it means you can rely on a
function being _total_ to always return a result, or that you can use a
_partial_ function, for example, to filter values out (see for example, the
Scala function `collect` which uses a partial function to do a combined
map/filter operation).


# Wrap Up

Next time, we'll take a bit of a side step and look at function currying and
function partial application, before taking the next step deeper into FP.

<span class="next">[Next in this series: Currying and Partial Application →](/articles/Currying-and-Partial-Application/)</span>

[^1]: It's worth to note here that the properties of a function being _partial_ is completely unrelated to the idea of _function partial application_. When looking for resources on either, make sure not to confuse them.
