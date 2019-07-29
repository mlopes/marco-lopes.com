---
layout: post
title: Expressing Effect Types
date: 2019-07-08 20:43
comments: true
published: true
tags:
    - scala
    - haskell
---

In the last chapter we talked about abstracting over effect types. We've
mentioned that concrete effect types were both too powerful, they let us do
too much, and also too weak, they carried a limited set of functionalities.
We've seen already how to restrict the functionality of the effect type, now
we're going to look into how to extend its functionality.

## MTL

MTL stands for Monad Transformer Library. Before going deeper into what this
is, and how you can use it, let me start by saying that the merits of using
MTL style are highly debated. I've been assured by people much more
experienced in Haskell than myself, that MTL is considered an outdated style
and has been falling out of favour in the Haskell community. In the Scala
community, it seems somewhat more popular at the moment, although some argue
about its performance on the JVM. Now, with disclaimers out of the way, let's
see what's this all about.

Monad transformers exist to solve the problem of composing monads of different
types. As usual, we're not going into the details of what are monads, or monad
transformers, as there's plenty of resources about that already, but we're
going to talk about the mechanics of it instead.
Some popular monad transformers are `ReaderT`, `StateT`, `OptionT`, `EitherT`,
or `WriterT`. Using a Scala example, if we have a `List[Either[E, A]]`, then we
could instead have an `EitherT[List, E, A]`.
Although this may look similar, with the first example, if we wanted to map,
or flatMap over the `Either`, we would have to first do it over the `List`.
With `EitherT`, we could do it directly.

How does this relate to effects then? 

## Monad Stacks

Monad transformers are often used to create what are called monad stacks,
which basically means nested monad transformers, with a monad at the end of
the nesting stack. So, if you want to pass around some context with your
effect type, you could have a stack with a `ReaderT[F, C, A]` where `C` would
be the type of your context, and `A` the type your effect will produce. If you
wanted to pass some state around, you could extend your stack to also be a
StateT, this is usually done via declaring type aliases or using the
`kind-projector` plugin, as the syntax for this kind of thing in Scala tends
to get ugly really quickly.

The problem with this approach though, is that code complexity increases
greatly and all of this is a convenience layer, as you can still pass state or
context around explicitly and keep your effect type clean and decoupled from
this extra functionality.

It's also worth mentioning that there's also a library called
[cats-mtl](https://typelevel.org/cats-mtl/getting-started.html) which provides
type classes for the more common monad transformers that allow you to
constraint your effect type so that your whole application still only knows
about an `F` for which some functionality is available.

##  Custom Effects

Another way to extend an effect type, is to create type classes for the
specific type of effect you're performing. Let's say you'r application
accesses a database, you could have a `DbTransactor[F]` type class, have an
instance for the effect type you are using, and then constrain your type
`F[_]: DbTransactor[F]`. There's an argument for doing this, some people say
it's expressive in the sense that it tells you what kind of effect your `F` is
capable of performing, which is more specific that just saying it can delay
effects. This argument comes from the fact that in Scala functions are not
inherently pure, and `Sync` is a type class that carries the very generic
information that this function intends to delay an effect. Again.

## Conclusion

If this chapter feels somewhat vague, that's because it is. It talks about
techniques that can be applied to achieve certain goals, but those techniques
have arguable benefits, and cause coupling, even if it's not tight, between
the effect type and, in the case of the transformer stack domain concerns (the
concern of passing around some context, like a configuration, for example, in
the case of a ReaderT), and infrastructure concerns in the case of the custom
type classes for effect types.

So, while I felt these are worth mentioning, and knowing about, they're kind
of lateral to the intentions of this tutorial, which is why this chapter is
almost like a side note of the main text.

In the next chapter, we're going to talk about tagless final.
