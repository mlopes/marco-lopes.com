---
layout: post
title: Type Parameters
date: 2019-05-10 18:59
comments: true
published: true
image: /assets/posts/images/parameterisedTypes.jpg
tags:
    - scala
    - haskell
    - learning
    - functional
    - fp tutorial
---

<span class="prev">[← Previous in this series: Currying and Partial Application](/articles/Currying-and-Partial-Application/)</span>

In the previous installments we've went into some detail about currying and partial application.
Before we move on to talk about effects, let's take a quick look into what are
type parameters.

![Parameterised Types](/assets/posts/images/parameterisedTypes.jpg){: .center-image .img-responsive }

Type parameters, are a way to abstract over types in what is called parametric
polymorphism.
The idea of type parameters, has some traits in common with the concept of
generics, which you may know from languages such as Java or C#.

Type parameters allow us to express the idea that a type can depend on another
type for which it doesn't have deep knowledge of (or often, any knowledge at all).
A common example would be, for example Haskell's `Maybe a` or Scala's
`Option[A]`, where `Maybe`/`Option` are parameterised by a type variable, that
can be made concrete at call time, for example `Option[Int]`.

Up to now, this sounds remarkably similar to generics, so why all of the
different terminology? To answer this question, let's take a deeper look at
the aforementioned `Maybe`/`Option` types.

Because the way to implement ADTs in Scala is in terms of inheritance, the
properties we're looking into will not be immediately evident, so let's see
a simplified, stripped down version of the Haskell implementation, would look
something like this:

```haskell
date Maybe a = Just a | Nothing
```

In Scala it would look like:

```scala
sealed trait Option[A]
final case class Some[+A](a: A) extends Option[A]
final case object None extends Option[Nothing]
```

The right side of the `=`, contains what are called data constructors, `Just a`
and `Nothing` in this case. They are named data constructors because when
called, they construct values of the type `Maybe`.

On the left side, you have the type constructor. See, in the previous
paragraph I called `Maybe` a type, well, I lied, `Maybe`, by itself is 
not a type. You can think of `Maybe` as a function, and of `a` as the
parameter of the maybe function. In fact, in Haskell, `Maybe` **is** a
function, and `a` **is** a parameter to that function. As with a traditional
function, when you pass a value to it, it evaluates into a result value, in
this function, when you pass a type to it, it evaluates into a result type.
So `Maybe a` is a type constructor, and when you pass a type to it, such as
`Maybe Int`, it constructs a concrete type, which is similarly named `Maybe Int`.

This might seem at odds with the common non-parameterised types, but in fact,
it isn't. Let's look for example into the type `Boolean`:

```haskell
data Boolean = True | False
```

`Boolean` is the type constructor, just like `Maybe a` was the type
constructor in our previous example. Even though `Boolean` doesn't take any
parameters, it still is a function called `Boolean` that when called, returns
a type, also named `Boolean`.

The same way a function that works at value level can have type `a -> a -> a`,
a function that works at type level can be said to be of a certain kind. Kinds
are like types for types, and in fact, as far as I'm aware seem to be
implemented the same way in more recent versions of Haskell.
So, what would be the kind of `Boolean` and `Maybe a`?
`Maybe a` is a function that takes one type, and returns one type, so, its
kind is `* -> *`. `Boolean` takes no parameters, so it has only a return type,
its kind is therefore `*`. A type constructor with two parameters would have
kind `* -> * -> *` and so on.

This is how the intuition for type parameters differ from simple generics. I
can't say if the Scala implementation actually represent type constructors as
type level functions, as it is constrained by the types as they exist in the
jvm, but certainly the intuition applies.

Having this intuition of type constructors and their behaviour as type level
functions, is helpful in understanding more complex use cases of type
parameterisation.s

In the next part, we're going to talk about a specific parameterised type that
has some properties that allows us to deal with effectful computations in a
pure way. We're going to take a first look at an effect system.
