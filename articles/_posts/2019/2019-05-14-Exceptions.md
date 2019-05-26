---
layout: post
title: Exceptions
date: 2019-05-14 21:06
comments: true
published: true
image: /assets/posts/images/BurnTheToast.jpg
tags:
    - scala
    - exceptions
    - good practices
    - architecture
    - design
---

# Not so Exceptional

I'm not keen on exceptions. When it comes to writing code, there are not a lot
of situations that can be considered exceptional, and certainly a lot less
than one would thing, looking at the amount of exceptions thrown by the
average codebase.

Error situations are better handled through the type system, and the act of
throwing creates this whole ortogonal flow to the one created by the call tree
of a programme.

On top of this, if you're using an effect system, it is likely that the effect
type is able to express error situations through the effect type. In Scala,
if you're using cats, you'll likely have `ApplicativeError` and `MonadError`
instances for your effect type, meaning there's even fewer reasons to throw
exceptions.

![An Exception Has Occurred: You've Burnt The Toast](/assets/posts/images/BurnTheToast.jpg){: .center-image .img-responsive }

# Semantic Dissonance

A bit of a plot twist here, is that recently, I have come across a situation where
it could have made sense to allow for the possibility of an exception to be thrown
in the code, if just the library I was using was built in a slightly different
way.

For context, let me describe the circumstances in which this happened.
Recently I started developing a library for idiomatic representation of date
and time values in Scala. The idea is that it alows representation of these
values in a way that is type safe, can be pattern matched, etc.

In order to ensure type safety, values that are usually represented as
integers, are made safe using [refined](https://github.com/fthomas/refined)
types. So, for example, if you want to create an `Hour`, you can do it by
calling the constructor that accepts a refined `Int Refined Interval.Closed[W.`0`.T, W.`23`.T]`
(type aliased to `NumericHour` in the library, for brevity and expressivness),
and this will yield an `Hour`, or you can create it from an `Int`. Because you
can't guarantee that an `Int` is a valid hour, this constructor will yield an
`Option[Hour]`.

Up to now, everything is good, and we can handle things through the type
system. But lets now talk about `Month`. Months are represented by case
objects, named after each month, I.E `January`, `February`, etc..., all
extending a `Month` trait. Now, the interesting bit here, is that it also
makes sense to represent months as integers, because in our daily lifes, we
also refer to months as `1` to `12`. So, we follow a similar pattern. We have
a `fromInt` constructor that returns an `Option[Month]`, and so if we call
`Month.fromInt(1)`, we get `Some(January)`. And then we have the constructor
that creates a month from a refined type. Fortunately, you can pattern match
on refined types, so we can write an elegant solution for this problem, which
I am not sure forces the match to be exhaustive. So, let's imagine we
couldn't, because in dealing with integers we can't avoind the problem.
How could we get a `Month` out of this integer, which we know for sure is a
valid number between `1` and `12`?
In this situation, there wouldn't be much more that we could do, other than
extracting the integer value from the refined type and construct the `Month`
using the integer type.

But, constructing a `Month` from a `Int`, returns an `Option`, and since we
know that we only have valid values and cannot therefore have a failure to
construct a `Month`, we have *semantinc dissonace*. In other words, the
semantics of `Option` is a type that represents the existence of 0 or 1
values, but we have a situation where `None` cannot occur, so we can only ever
have 1 value.

# Let it be Exceptional

At this point, I would argue this would be a valid use case to directly call
`get` in the `Option` we get back from out `fromInt` call. The reasoning
behind this, is the following:
We don't want to expose this semantic dissonance to the library uses, so we
should extract the value out of the `Option`. By doing this, we are risking
throwing an exception to happen, but this code path that triggers the exception
should not be possible to trigger. On the other hand, if it does happen that
we missed something and there is a situation where this code path happens, we
don't want the user to be tricked into thinking that the library worked, we
want them to immediately see that something went wrong, and let us know so
that we can fix it (or better yet, them making a pull request ;) ).

This is, in my opinion, the reasonable use for an exception, to deal with a
codepath that for some reason exists but, as far as we are aware, should never
be triggered. If for some reaons it is triggered, that's really exceptional. 

This being said, I have yet to come across a real life scenario where this
happens, and it's not just caused by some bad architecture decision, and by
fixing it we also remove the need for the exception.

