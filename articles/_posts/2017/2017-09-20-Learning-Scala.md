---
layout: post
title: "Learning Scala"
date: 2017-09-18 22:00
comments: true
published: true
tags:
    - scala
    - haskell
    - learning
---

## How I Got Into Scala

This article describes a list of resources I've used to find my way into Scala development. For context of how relevant these resources might be to you, let me first give a quick description of my technical background.

## A Little Background
I've been a developer for a very long time, and I have quite a lot of experience. I've done some Perl, years ago where I got familiarised with mapping, filtering and reducing, but, even though I've been interested in functional programming for a long time, I've never really had the time or will to really dive into it.

This being said, lets get into the details about the resources and how they worked for me.

## Functional Programming Principles in Scala<span class="reference">[[1](https://www.coursera.org/learn/progfun1 "Functional Programming Principles in Scala")] </span>
Given the background described above, it comes as no surprise that I started my Scala experience by attending the online course Functional Programming in Scala, delivered by Martin Odersky on Coursera.

The course starts very easy, for someone with previous experience as a developer, and really only starts becoming a bit harder around the 4th week. This doesn't mean that, if you don't have previous experience with Scala, you can skip the first weeks, as they introduce some basic concepts of the language and of functional programming that are required to be able to progress further.

Even though the course is very good and teaches some very important language and paradigm concepts, its syllabus is very academic and in the end I was left with a feeling that I had learned a bunch of concepts, but didn't really had enough to actually do some real work with the language.

## Scala Exercises Website<span class="reference">[[2](https://www.scala-exercises.org/ "Scala Exercise Website")] </span>
I have to confess, I didn't use this site that much, I've tried it for a bit, but never really got into it. The std lib exercises tended to feel academic, just like the ones in the Coursera course, and a lot of the examples seemed to lack context and some information seems to be known by the authors and assumed that everyone else will know it as well, making it hard to use as a learning resource. 

Note that this is an Open Source effort and may therefore improve rapidly, so in spite of my sub-optimal experience, it's definitely worth to check this site. Also, it has exercises in a bunch of different widely used Scala libraries, such as Cats, Shapeless or Circe.

## Learn You a Haskell for Great Good<span class="reference">[[3](http://learnyouahaskell.com/ "Learn You a Haskell for Great Good")] </span>
"What is a Haskell book doing in here?", you ask. Well, a lot of people in the Scala community will point you towards this book, if you ask for a resource to learn Scala. Actually, a lot of people in any community for a functional language will point you towards this book.


At first I was reluctant to read it. I thought that if I wanted to learn Scala, the last thing I wanted to do was to have to learn some other language's syntax instead of investing that time in some Scala resource. Well, I was wrong. Reading this book helped me more in understanding a lot of concepts that are ubiquitous in Scala than any other Scala learning resource I had looked into before.  
Additionally, it made me really like Haskell, which is something I never though I would before.


The book is really well written, with a light tone, and everything is well explained and made clear. It explains concepts like functors, applicative functors, monads and monoids, in an understandable and unpretentious way, that makes them sound simple, and is easy to learn.


Learn You a Haskell is [available online](http://learnyouahaskell.com/chapters) as a html website, or can be [bought](https://www.nostarch.com/lyah.htm) in a variety of formats. Additionally, you can generate a kindle version from [this](https://github.com/igstan/learn-you-a-haskell-kindle) GitHub project.

## Functional Programming in Scala(red book)<span class="reference">[[4](https://www.amazon.co.uk/Functional-Programming-Scala-Paul-Chiusano/dp/1617290653/ "Functional Programming in Scala")] </span>
I've never managed to finish this one. It's the exact opposite of Learn You a Haskell. It sounds pretentious, it mixes concepts that are completely out of place immediately in the first chapters, just to tell you not to worry about them yet because they're too advanced for you. Personally, it makes me feel bored and I tend to want to put it down and go do something else.
This being said, a lot of people swear by it, so might still be worth it to take a look and see what you think.

## Exercism<span class="reference">[[5](http://exercism.io/languages/scala/about "Exercism.io")] </span>
I didn't knew about exercism until very recently, but I'm surely glad I found out about it. Exercism has exercises in a variety of languages, including Scala. And even though I have to confess I've mostly focused on the Haskell ones, these are really helpful exercises that stray from the typical "make a function that calculates a Fibonacci sequence" cliche. The exercises are interesting and varied, users can look at each other's solutions and comment, and you can join or create teams.

## The Neophyte's Guide to Scala<span class="reference">[[6](http://danielwestheide.com/scala/neophytes.html "The Neophyte's Guide to Scala")] </span>
This was introduced to me as a good starting point to learn Scala. It isn't. Don't get me wrong, this is one of the best resources to learn some Scala. It just isn't exactly for "starting". As it describes itself:

> The Neophyte's Guide to Scala is a book for intermediate Scala developers.

The first time I tried to read this, I didn't go further than the 1st part. I had no idea what was going on there, and learned nothing out of it. After some time, having looked into some of the previously mentioned resources, I came back to this. I was thoroughly convinced that I was going to hate it, and I would have to persevere through it. But to my surprise, I was now finding it very enjoyable, could understand its contents and was learning a lot about the details of the language.

If you've already managed to learn some Scala and have used pattern matching, options, eithers, futures, know what currying is and understand it, then this is for you. It will go into more details on each of these things and give you a greater understanding of how they work in Scala.

This is probably the perfect companion to the Learn You a Haskell book, as it will show you the "Scala way" for a lot of the concepts that you can learn on that book.
It can either be read on the [author's blog](http://danielwestheide.com/scala/neophytes.html) online, or [bought](https://leanpub.com/theneophytesguidetoscala) in a variety of formats, at a price defined by you.

## Other resources
I haven't had the opportunity yet, but there's another Scala book that I've been told is very good and much more practical that the red book, which I want to take a look at, at some point.

Creating a real application using Scala, was easily the most efficient way of learning Scala, and understanding how to use it for a real life situation (as opposed to the more academic maths exercises found in resources like the Coursera course).

## Conclusion
Those are the resources I've used to get myself to learn some Scala and start writing programs with it. Of course your mileage may vary but hopefully this list of resources will help you, find your way.
