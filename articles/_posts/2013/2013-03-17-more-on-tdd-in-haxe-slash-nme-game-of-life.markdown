---
layout: post
title: "More on TDD in Haxe/NME - Game Of Life"
date: 2013-03-17 20:02
comments: true
tags:
    - haxe
    - nme
    - tdd
    - games
    - munit
---

Recently I came across the [**Game of Life**](http://en.wikipedia.org/wiki/Conway_game_of_life) **code kata**, through [Marcello Duarte](https://twitter.com/_md), while doing some TDD practice in PHP.
Due to the visual nature of this *game*, I decided to give it a try using Haxe. I had already done it in PHP a few times, and had established that there was nothing too complicated on the test, that would give me trouble with munit, or would require mocking (still haven't looked into mocking frameworks for Haxe ;) ).


My first effort to do it in Haxe/NME is available on github (<https://github.com/mlopes/NME-GameOfLife>). This post will focus on how that code was created.

# TDD #

I tried to apply the principles of TDD and code it as if doing a **code kata**, so:

* Write a test
* Watch the test fail
* Write **just enough** code to make it pass or to change the failing message
* Refactor the code and tests, making sure the tests still pass
* Go back to step 1

While doing the kata, always follow these principles. If you need some code to account to some odd situation that just occurred to you, write a test for it first and follow those 4 steps in that order.

## Write a test ##

The first [rule](http://en.wikipedia.org/wiki/Conway_game_of_life#Rules) of the **Game of Life** as described in Wikipedia says:

> "Any live cell with fewer than two live neighbors dies, as if caused by under-population."

So, I decided to start by implementing this rule. In order to do this, I described my code as doing the following:

> It returns false for live cell with less than two neighbors

This description of the code is used as the name of the first test, which then proceeds in describing how this will be achieved:

```java

    ...

    import org.games.life.Life;

    ...

    class LifeTest
    {
        ...

        public function itReturnsFalseForLiveCellWithLessThanTwoNeighbours():Void
        {
            var life:Life = new Life();
            Assert.areEqual(0, life.getNextGenerationCellState(1, 0));
            Assert.areEqual(0, life.getNextGenerationCellState(1, 1));
        }

        ...
    }
```

What to do next? Let's start writing some code!!!

**WRONG!!!**

## Watch it fail ##

Now, we're going to run our test and see it return red, which means it didn't pass. There's a bunch of reasons to do this. First to see if it fails for the right reasons, second to see if it fails at all, third to keep yourself into this rhythm of these 4 steps.
Running the test will now complain that the class Life does not exist, so let's create it.
Now, because you just fixed the problem generating the failure message, you'll need to run your test again. Do it, and you'll see the message changing to say that the getNextGenerationCellState method does not exist. The test is still failing, but the fact that the message is now different, shows we're on the right track. We should now create the method and our test will finally fail because 0 is not equal to whatever our empty method is now returning.
Now we write the simplest piece of code that can make it pass, which is basically this:

## Write just enough code to make it pass ##

```java
    ...

    public function getNextGenerationCellState(parentCellStatus:Int, numberOfNeighbours:Int):Int
    {
        return 0;
    }
```

You might say, that this doesn't do what we want, but it does. Right now the only think our code needs to do is to return 0 in a given situation, the description (our tests) doesn't even say it should not return 0 when the cell has 2 or more neighbors.
Is this useful? Not at all, but we're not done with our tests yet. So let's leave it as this now.

## Refactor ##

We can now look at our code and refactor it. Because we're only on the first iteration and both our code and our test are very simple, I don't think there's anything to change here, which lead us back to step 1.

## Write a test ##

So on to the second rule, which I described in the code as:

> It returns true for live cell with two or three neighbors

And to test this:

```java

    public function itReturnsTrueForLiveCellWithTwoOrThreeNeighbours():Void
    {
        var life:Life = new Life();
        Assert.areEqual(1, life.getNextGenerationCellState(1, 2));
        Assert.areEqual(1, life.getNextGenerationCellState(1, 3));
    }
```

## Watch it fail ##

Run the test, and see it fail for the right reason, which is the test expected to receive 1 and received 0. Not really surprising, since our code only returns zeros, for now.

## Write just enough code to make it pass ##

Now we will write again the minimal amount of code to make the test pass:

```java
    ...

    public function getNextGenerationCellState(parentCellStatus:Int, numberOfNeighbours:Int):Int
    {
        if(numberOfNeighbours == 2 || numberOfNeighbours == 3) {
            return 1;
        }
        return 0;
    }
```
And we now stop writing code, because our test passes. Again you'll notice this is still not the ideal implementation, but it moved one step closer to it. Don't worry, while writing tests and making them pass, and then refactoring your code, you'll get to the point where it does exactly what it needs to, and because of the refactoring, it should do it in a very clean and simple way.

## Refactor ##

Again, in the code there seems to have nothing worth refactoring yet, but now we have `var life:Life = new Life();` repeated in our tests, which means we should refactor them.
Create a property called life, instantiate it in the test setup method, and remove the object creating from the test methods.

## And so on... ##

Now repeat these steps, until all the behavior is described in a test, and all the tests pass.

# Consumer #

In this example we have two more classes that we haven't talked about yet: Game and Main.

Main.hx is the kind of file that comes with NME project templates, it's the entry point to the application. As such, it's not owned by us, and thus we should not test it.

Game is owned by us, so why is it not tested? Well, the Game class is not part of our game API. It is the consumer for our API, and it should be tested, but not by unit tests but rather by acceptance tests, which is out of the scope of this post.

# Well... that's it, pretty much #

Take a good look at the test, and how they make the implementation evolve, give it a try to some **code katas**, practice TDD as much as you can, because you'll see the benefits in your code every day. Remember that the way you approach the problem should be your own, and don't let this example stick you to a certain path. Any questions, suggestions or improvements to this post, please, let me know.

<iframe src="/assets/posts/js/GameOfLife/" style="width: 640px; heigth: 480px"></iframe>
