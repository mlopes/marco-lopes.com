---
layout: post
title: Abstracting Over Effect Types
date: 2019-06-21 20:16
comments: true
published: true
tags:
    - scala
    - haskell
    - learning
    - functional
    - fp tutorial
---

In the previous chapter we talked about effects, how to use them, and what are
they useful for. We chose one of the available effect types, and saw that we
can do all kinds of things with it, we can map or flatmap over it, we can use
it to suspend side effects, we can usse it to lift pure computations into the
effect type so that we can compose those computations with effectful ones, we
can even use it to run the so called "unsafe" operations and force the side
effects to happen making our code not pure.

On one hand, our effect type is extremely powerful and often allows us to do a
lot more than we need, or even desire to make possible in certain parts of the
code, but on the other hand, it is also "closed", in the functionality that it
carries with it. In the case of our example, we were stuck with whatever
functionality `cats.effect.IO` provides, and that's it.

# Principle of the Least Powerful Abstraction

The principle of the least powerful abstraction says that your code should
lean on the least powerful abstraction possible that it requires, in order to
be able to do what it needs. So, for example, if we an `IO[String]` and we
want to perform a computation on it that will return an `IO[Int]`, we only
need an abstraction that provides us with `map`, and yet we have a concrete
implementation that provides us with `map` but also a lot of other powerful
tools that we don't intend to use, and may even want to make sure others won't
go behind our back and use them (for example the `unsafe*` methods of `IO`).
That means we're violating the principle of the least powerful abstracting by
giving this piece of code a lot more power than it needs to.

In case it's not clear, there are several benefits to constraining the power
of the abstractions in scope. The first one being that it makes intentions
explicit, if I write a function that takes a `Functor`, you know by this
information alone that it can only `map`, but if I write one that takes an
`IO`, you can tell nothing about it. The other major benefit is that it also
leaves less scope for us to shoot ourselves in the foot, using the `Functor`
example again, you can't run any of the `unsafe*` methods on `Functor` but you
can with `IO`.

An example that might ring with more developers is transforming a list using
`map` vs transforming a list using a `foreach` loop. Using `map`, the only
thing you can to is apply a function to each element of the list, and you will
get a list of the same lenght with the result of the function applied to each
element of that list. `foreach` on the other hand, is infinitely more
powerful, there's virtually no limits to what you can do with it, leaving you
in a situation where it's not immediately clear what the code does, and where
there's a lot more room to introduce bugs.

# Type classes

So where does this leaves us? Why am I comparing `IO` with something called
`Functor` and what is even this `Functor` that we're talking about?

On a bit of a side note, let me just say that I have no intention of going
into an explanation of concepts of category theory, and my previous question
is about what is `Functor` and not what is *a* functor. If you want to learn
more about category theory, I recommend reading [Learn You a Haskell for Great Good](http://learnyouahaskell.com/chapters)
which is also available as a [paper book](https://amzn.to/30ZWcU1), or as a
[kindle book](https://github.com/igstan/learn-you-a-haskell-kindle). If you're
interested in understanding the maths behind it as well, I recommend Bartosz
Milewski's [youtube videos](https://www.youtube.com/watch?v=I8LbkfSSR58&list=PLbgaMIhjbmEnaH_LTkxLI7FMa2HsnawM_),
and also the [blog posts](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)
which are available as a [paper book](https://www.blurb.com/b/9008339-category-theory-for-programmers)
as a [kindle book](https://github.com/mlopes/category-theory-for-programmers-kindle)
or as a [pdf](https://github.com/hmemcpy/milewski-ctfp-pdf/).

We've talked about [type parameters](/articles/Type-Parameters/) before, and
at the time, we kept them completely generic, for example, we mentioned
`Option[A]`, what do we know about `A`? We know absolutely nothing about it,
right? Let's say we were implementing `map` for `Option`, what could we do
with value of type `A`? Since we know nothing about `A`, we certainly can't
call methods on it, and since `map` takes a function from `A -> B`, that's
basically all that we can do, apply that function to the value of type `A` to
get a `B`. So, `A` is completely polymophic, it can be replaced by any type,
but it also means that there's very little you can do with it (not necessarily
a bad thing though).

Is there a way we can have a type parameter and know something about it, then?
Well, yes, that's where type classes come in. Type classes are another way to
achieve polymorphism, also known as late binding ad hoc polymorphism, in
contrast to parametric polymorphism (our `A` in `Option`), and early binding
ad hoc polymorphism (inheritance). In Haskell you define a type class using
the keyword `class`, you can then define type class instances with the keyword
`instance` or, if it's possible to derive the type class for a type, using the
`deriving` keyword. In Scala this can be pretty ugly, and if you want to know
how to write your own type classes I recommend [this blog post](https://danielwestheide.com/blog/2013/02/06/the-neophytes-guide-to-scala-part-12-type-classes.html)
by Daniel Westheide.

The intuiton for a type class can be, in my opinion, better explained by
comparison to inheritance. In inheritance, a type is polymorphism by
implementing a "is a" relationship, you create a class (in the OO sense of the
word, not in the type class sense of the word), and during its declaration you
say that this class **is a** something, ex: `class Cat extends Mamal`, you
say `Cat` is a `Mamal`. This is early binding polymorphism, as the
relationship is defined at declaration time, once the class has been declared,
you can't say that it is anything more. With type classes the relationship is
"has a", so it's late binding, the type is defined without knowledge of the
type class, and an instance of the type class is provided at any point, and as
long as there's an instance in scope (the type **has a**), then the 
functionality provided by the type class is available for this type.

Back to the above example of `Functor` and `IO`, `Functor` is a type class,
and, in the `cats` ecosystem, there is an instance of `Functor` for `IO`.

# Type Class Constraints

We talked about type parameters and type classes, but we haven't looked into
how they're used together yet. Let's do that now.

If we have a type class `MyTypeClass`, and we have some type `MyType`
parameterised on `A`, `MyType[A]`, we can say constrain `A` to be a type that
has an instance of `MyTypeClass` available in the scope where `MyType` is used
by declaring it like this `MyType[A: MyTypeClasss]`. And now, we know that `A`
supports whatever functionality `MyTypeClass` provides. So `A` is less
polymorphic that the `A` in `Option`, but it also means that by making it less
polymorphic, there's more we know about it and more we can do with it.

We've talked before about type constructors are functions, and that some types
have kind `* -> *`, also known as higher kinded types. `IO` is one such type
constructor. In Scala syntax, this presents us with a problem, when trying
to define a type parameter where we can "fit" `IO`. If we look at `MyType[A]`,
we see that `MyType` is a type constructor that expects a type to take the place
of the parameter `A` and make a concrete type, so we couldn't pass `IO` as a
parameter to `MyType`, because `IO` is not a concrete type, but rather a type
constructor (we could pass `IO[String]` for example, as this is a concrete
type). To work around this, Scala allows us to define higher kinded type
parameters with the syntax `MyType[A[_]]`, although by convention we wouldn't
use `A` but rather `F`. Now, the `MyType` type constructor requires a higher
kinded type as a parameter.

So, if we have `MyType[F[_]]`, we can now contrain it to a type class, like
`Functor`, so that we end up with something like `MyType[F[_]: Functor]`. Now,
since `IO` is a higher kinded type, and it has an instance of `Functor`, we
can pass `IO` to `MyType`. Inside of the abstraction encapsulated by `MyType`,
we don't know that it is being used with `IO`, and it might as well be used
with something else, and we are now also limited to calling the functionality
of `Functor` inside of it, so we can't suspend side effects or run the unsafe
methods on it. We also know these things just by looking at the signature of
`MyType` and be sure that it won't do more than map over our `IO` (or whatever
other type with an instance of `Functor` we pass to it).

A breakdown of the type class hierarchy in cats can be found in the
documentation, [here](https://typelevel.org/cats/typeclasses.html#type-classes-in-cats)
and the most common ones to depend on when dealing with effect types will
likely be `Functor`, `Applicative`, `ApplicativeError`, `Monad`, `MonadError`,
and `Sync`, in order from the least to the most powerful.

# F[_]

This is what it means when people say that they write an application in `F`,
it means that at the entry point of your application you instantiate a
concrete effect type, but from that point on, everything relies on a less
powerful abstraction, preferably an unconstrained `F[_]`, but more often than
not, an `F[_]` that requires one of the above mentioned type classes.

In the next chapter we're going to discuss advantages and disadvantages of
adding functionality to `F[_]` using MTL style and type classes.
