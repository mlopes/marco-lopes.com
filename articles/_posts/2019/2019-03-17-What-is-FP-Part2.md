---
layout: post
title: "What is Functional Programming - Part 2?"
date: 2019-03-17 10:00
comments: true
published: true
tags:
    - scala
    - haskell
    - learning
    - functional
    - ft-tutorial-series
---

Previous in this series: [What is FP?](/articles/What-is-FP/)

In the previous chapter, we've established the very basics of what is FP, and
tried to dig a bit deeper in what "programming with functions" means.

In this chapter we're going to try to establish what functions are, how
they're defined differently in different domains and what do we mean by
_function_ in functional programming.

# What are functions?

In programming, the idea of function differs greatly from the idea of a
function in mathematics, so let's first try to establish this difference.

## Functions in programming

In programming, what is normally called a _function_ is a subroutine. This
basically means it's a (somewhat) closed code block containing sequences of
language instructions. These _functions_ can take parameters and can return
values, which might be solely based on the input parameters or not.
An example of this, using Scala, could be (please ignore all the horrible ways
this code could go wrong, the deprecation, and all the unecessary steps that exist
here for the sake of simplicity and example):

```scala
def foo(x: Int) = {
  val y = readLine("Enter a number: ")
  val n = x + y.toInt
  println(s"$x plus $y is {$n.toString}")
}
```

This would read some input from STDIN, convert it to an integer, add 2 to it,
and then print it to STDOUT.


## Functions in maths

Because the objective here is to learn about FP, not maths, I'm goint to try
to focus on how they differ from the concept of _function_ that developers are
more familiar with, rather than anything else.
In maths, functions are mapping from a set o values to another set of values.
This boils down to being an expression that can be reduced to a value, by
applying the parameter values and reducing the expression.
A representation of this, in programming, using Scala syntax could be something
like:

```scala
def add(x) = x + 3
```

What this implies is, if you replace `x` with a value, let's say `2`, you can
perform variable substitution in the expression, which gives you `2 + 3`, and
then you can reduce it, which will give you a value, `5`.

## functions vs functions

In our first example, we see that we have a sequence of instructions, while on
our second example we have only a single expression. We also see that
for `println`, we don't even care about its return value, and furthermore for
`readLine` we don't depend on the input parameters but rather on runtime user input.
This means that there's a lot of things going on in our programming function that
couldn't happen in the maths definition of functions, it being a mapping between sets
of values.

This brings us to the difference between an expression and a statement.

# Expressions vs Statements

Well, there's really not a lot to say about this at this point. In `x + 3` we
can replace the variables by the function parameters, and reduce the
expression by applying the operations on the values until we have a single
value. In `println(s"$x plus $y is {$n.toString}")` we're giving an instruction
to perform some output, we can't apply variable substitution, reduce it, and
in languages that actually implement it as a statement get a value out of it
(Scala does return a value and implements it as a function, but more on that 
on a future installement).
The former is an _expression_, the latter a _statement_.

# Why is this distinction useful?

I'm glad you asked. There's a number of reasons why this distinction is useful, let's talk
about some of them.

## Reasoning

If you can apply variable substitution and reduce an expression, and nothing
else, this gives you a very powerful tool to reason about that expression.
Armed with this knowledge, you can establish the mapping between the input and
output values. This is not true for statements, as they might depend on third
parties (such as user input), or not even do a mapping at all.

## Local Reasoning

The previous property also gives you what's often called local reasoning. If
all you can do is apply variable substitution and reducing by applying the
operations in the expression, you don't need to keep other parts of the system
in your mind. Again, this is not possible with statements because they depend
on the outcome of the instruction given by the statement.

## Partial Reduction

This one might be a hard one to understand if you've not come across this sort
of thing before, but bear with me for a second.

Let's say you have the following expression:

```haskell
x + 2 + y
```

Let's also say that you know that `x = 5` but you don't know the value of `y`.
You can still apply variable substitution, and so you get `5 + 2 + y`. But if
you look at the resulting expression, you might see that we can still do some
reduction by applying the `+` operation, resulting in `7 + y`. Because you still
don't know `y`, you'll have to stop here. This means that by reducing an expression
you can also get a simpler expression, not just values.

## Every input maps to an output

If an expression can always be reduced into a simpler expression or a value,
it means that we know that there's never a situation where we get nothing out
of an expression. This might sound like a no-brainer, but being able to know
that we always get something that we can use, is an important property that we
can rely on.

## Composability

Expressions are combinations of values and operations on those values, which
can eventually be reduced into other values. This means you can create more
complex expressions by composing simpler ones (ex: `2 + (5 * x) - (2 / y +
z)`).

## Perfom outcomes

This is where statements come into. If an expression allows you only to
perform variable substitution and operation application, then you can only
transform them into simpler expressions or values, but you can't make them
cause a change in the world.


# Functions in programming

We see by the previous examples, that in general, in programming languages
functions can be made of expressions, statements or combinations of both.

In Pascal, there used to be two types of subroutines, the *function* and the
*procedure*. The main difference between the two, is that functions have a
return value and procedures don't. Most languages don't make this distinction
and just return `void` instead.
While not enforced in any way in Pascal, it is useful to think about why functions
that have a return value are so different from the ones that don't, that would
warrant these two constructs.

It seems like procedures fit better with the model where we chain statements
to perform actions, and  we don't always (sometimes we do want an exit code or
similar, but more often than not, the last statement's output is ignored)
really care about a return value (look at our `foo` function, we don't really
return anything useful out of it), while functions fit better the model where
we define expressions and map from the input parameter values to the output values.

The lack of a first class construct to distinguish between those two different
things, is a huge hinderance in making developers aware of this difference.
Languages like Haskell address this difference in a way that we'll look into
in a future installement.

As a side note, these procedure like functions with no return type, usually
expressed through `void`, are usually handled through the type system in typed
functional languages, by returning a value of type `Unit`, which represents a
type that can have a single value (unit), and therefore carries no information.

# How does any of this relate to FP?

Remember on the previous chapter I said that FP is all about injecting
functions? Well, turns out that if your functions are made of these
expressions that depend only on the input parameters to generate their output,
then your functions also inherit those properties I described above (easy and
local reasoning, allow partial application, can be trusted to return something, 
and are composable). This means you can build complex behaviours by composing
smaller functions with simpler behaviours while being able to reason about each
of those functions individually.

## But then, how can I make the computer actually "do things"?

In most functional languages, functions are not inherently made exclusively of
expressions (they are in Haskell where functions are a single expression but it
provides constructs _where_ it _lets_ you set up auxiliary
values/expressions).

In most functional languages you are encouraged to separate functions that perform
these actions and push them to the edges of the system. This is, again, not
unlike the popular OO strategy when doing Domain Driven Development, of
modeling input and output sources as interfaces at the boundary of the system.

In Haskell, this is addressed explicitly, and by default you can only 
perform these actions inside of something called an effect system (Scala also
has a few effect systems available via 3rd party libraries namelly cats.effect.IO and ZIO). 
Effect systems are part of what makes functional programming so powerful.

However, effect systems are outside of the scope of this chapter, and will be
addressed in a later installement.

# Wrap Up

Next time, we'll talk about some properties of functions, which are very
closely related to the concepts we've talked about here.
