---
layout: post
title: The Case for Exceptions?
date: 2019-06-04 06:32
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
than one would think when looking at the amount of exceptions thrown by the
average code-base.

Expected error situations are better handled through the type system, and the act of
throwing creates this whole orthogonal flow to the one created by the call tree
of a programme. Also, if you are aware of a possible runtime occurrence, then,
there's nothing exceptional about it, and you should handle it through the flow
of the programme.

On top of this, if you're using an effect system, it is likely that the effect
type is able to express error situations through the effect type. In Scala,
if you're using cats, you'll likely have `ApplicativeError` and `MonadError`
instances for your effect type, meaning there's even fewer reasons to throw
exceptions.

![An Exception Has Occurred: You've Burnt The Toast](/assets/posts/images/BurnTheToast.jpg){: .center-image .img-responsive }

# Semantic Dissonance

I develop a developing a library for idiomatic representation of date
and time values in Scala. The idea is that it allows representation of these
values in a way that is type safe, can be pattern matched, etc.

In order to ensure type safety, values that are usually represented as
integers, are made safe using [refined](https://github.com/fthomas/refined)
types. So, for example, if you want to create an `Hour`, you can do it by
calling the constructor that accepts a refined `Int Refined Interval.Closed[W.`0`.T, W.`23`.T]`
(type aliased to `NumericHour` in the library, for brevity and expressiveness),
and this will yield an `Hour`. Optionally, you can create it from an `Int`,
and because you can't guarantee that an `Int` is a valid hour, this constructor
will yield an `Option[Hour]`.

Up to now, everything is good, and we can handle things through the type
system. But lets now talk about `Month`. Months are represented by case
objects, named after each month, I.E `January`, `February`, etc..., all
extending a `Month` trait. Now, the interesting bit here, is that it also
makes sense to represent months as integers, because in our daily lives, we
also refer to months as `1` to `12`. So, we follow a similar pattern. We have
a `fromInt` constructor that returns an `Option[Month]`, and so if we call
`Month.fromInt(1)`, we get `Some(January)`. And then we have the constructor
that creates a month from a refined type, `Month(numericMonth: NumericMonth): Month`.
Fortunately, you can pattern match on refined types, so we can write a somewhat elegant
solution for this problem.

I say "somewhat", because even though we can pattern match, it is unlikely that
this match is forced to be exhaustive, given that we're matching on `Refined`
of `Int`, which there's an infinity of possible values for (technically limited by
the size of Int). It is then possible that a value would be generated that we're
not matching against, because it would not yield a valid month. While technically
there's no way that we're ever going to get a value of `Refined` of `Int` that
is invalid, because _refined_ makes sure of that, theoretically we could still
get an invalid value, if either we failed to define the right refinement for month,
of if _refined_ happens to have some bug.

If our refined type is defined correctly, and there's no obvious bug on
_refined_, then we know for sure that we'll get a valid number between
`1` and `12`. Given this situation, then it makes sense to assume we
can do the following, where `NumericMonth` is `Int Refined
Interval.Closed[W.`1`.T, W.`12`.T]`:

```scala
def apply(numericMonth: NumericMonth): Month =
  numericMonth match {
    case Refined(1)=> January
    case Refined(2)=> February
    case Refined(3)=> March
    case Refined(4)=> April
    case Refined(5)=> May
    case Refined(6)=> June
    case Refined(7)=> July
    case Refined(8)=> August
    case Refined(9)=> September
    case Refined(10)=> October
    case Refined(11)=> November
    case Refined(12)=> December
  }
```

Here, we're not accounting for any of the possible integers that are not `1`
through `12`, so our program can effectively throw an exception at runtime.
We could make this returns an `Option`, have a default match for invalid
integers, and return `None` for those cases.
But, since we know our integer value comes from a refined type that only
allows for values between `1` and `12`, and therefore we can only have valid
integer values for month representation, we would have *semantic dissonance*.
In other words, the semantics of `Option` is a type that represents the
existence of 0 or 1 values, but we have a situation where `None` cannot
occur, so there will only ever exist 1 value.

# Let it be Exceptional

At this point, I would argue this would be a valid use case to let an exception
to happen at runtime.
We don't want to expose this semantic dissonance to the library users, so we
should ignore the impossible code paths, and keep them contained in our abstraction.
By doing this, we are risking an exception to happen, but this code path
that causes the exception should not be possible to trigger, so if it does, we have
in fact an exceptional situation, which indicates there's a bug either in our code, or
in _refined_.

Another benefit of allowing this exception, is that it also doesn't hide a possible bug.
If we return an `Option`, client code will handle the dissonance, so they'll expect cases
of `None`, but if we don't, then the client code will immediately show that something
went wrong.
At this point the library user can let us know so that we can fix it (or better yet,
they can make a pull request ;) ).

This is, in my opinion, the reasonable use for an exception, to deal with a
code-path that can't be prevented, but should never be triggered. If for some
reason it is triggered, that's really exceptional, and we want to know about it.
Anything else, is using exceptions for control flow, and it's a design smell.

