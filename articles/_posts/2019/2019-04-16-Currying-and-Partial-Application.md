---
layout: post
title: Currying and Partial Application
date: 2019-04-16 12:36
comments: true
published: true
tags:
    - scala
    - haskell
    - learning
    - functional
    - fp tutorial
---

Previous in this series: [Function Properties](/articles/Function-Properties/)

In the previous installements we've mentioned currying and partial application.
Let's try to give some clarity as to what those two things mean.

# Currying

Currying is a simple concept that is a bit hard to pass across, if you never
came across it before. Let's say you have a function `f` that takes 3 `Int`
values, `x`, `y`, and `z` and returns a value `r` of type `String`, so 
`def f(x: Int, y: Int, z: Int): String`. You can also think of it as a function
that takes `x` and returns another function that takes `y` and `z`, and returns
`r`, like `def f(x: Int): (y: Int, z: Int => r: String)`. In turn, you can do the
same thing with the return function and make it into a function that takes `y`, 
and returns a function that takes `z` and returns `r`, like 
`y: Int => z: Int => r: String`. So, you end up with a function
`def f(x: Int): (y: Int => (z: Int => r))`.
What we did here, was we broke down our 3 parameter function into 3 functions that
take 1 parameter each.
The possibility of treating functions like this was developed and prooved by
mathematicians, amongst them Haskell Curry, after whom the technique was later
named, as well as _Haskell_ the language.

## Uses of currying

In _Haskell_, all functions are curryied by default. This means that if you declare
a function with more than one parameter, behind the scenes that functions just takes
one parameter and returns another function that takes one parameter and so on.
In _Scala_, functions are not inherently pure, so you have to manually curry a function,
because if a function performs an effect the interpreter won't know if it can partially
apply it without breaking it's procedural order, for example, in the case of a function
that requests input from the user, that action doesn't depend on the function inputs, but
subsequent instructions on that function might. So, if you want a function to be curryied, 
you have to explicitly say so and take responsibility to not break it. The example above
would become `def f(x:Int)(y: Int)(z: Int): String`, meaning that the function is now curryied.

But why would we want to curry a function?
That takes us to the next section. Partial application.

# Partial Application

Next in this series, something something something
