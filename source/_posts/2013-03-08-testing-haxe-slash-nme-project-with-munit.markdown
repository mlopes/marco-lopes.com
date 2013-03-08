---
layout: post
title: "Testing Haxe/NME project with munit"
date: 2013-03-08 11:15
comments: true
published: true
categories: [haxe,nme,tdd,games,munit]
---

Anyone who ever used haxe as certainly noticed that it is a very promising language, with some very promising frameworks and libraries. NME specially is a very promising framework that didn't fail to amaze me on how good it is at getting a single codebase to compile to several platforms.
On the other hand, all of these projects seem to miss documentations and some guidance in general, and for someone who's suddenly interested in giving it a try, it can be a bit painful to get going. I experienced such a situation recently when trying to do some TDD with haxe.

# What to choose? #

The first problem to overcome was, choosing a testing framework. Haxe itself has a small native testing API, but it looked very basic, and so I decided to look for something else. The Haxe community doesn't seem much like a test driven community yet and it wasn't easy to find comparisons or documentation that would let me fairly compare any of the testing frameworks I came across.

I ended up choosing *munit* because it seemed slightly better documented and it also seemed to be an relatively active project.

# Installing and configuring munit #

The documentation on how to install it is pretty good, and install it ended up being quite easy. Just run the following command:

``` bash
$ haxelib install munit
```

Then, we need to configure munit for the project we want to test. Let's say our project is called MyAwesomeProject. From inside the project folder do:

``` bash
$ haxelib run munit config
```
    Massive Unit - Copyright 2013 Massive Interactive. Version 2.0.0
    Configure munit project settings
    --------------------
    test src dir (defaults to 'test') :
    output build dir (defaults to 'build') : deploy
    report dir (defaults to 'report') :
    target class paths (comma delimitered, defaults to 'src') :
    hxml file (defaults to 'test.hxml') :
    resources dir (optional, defaults to 'null') :
    templates dir (optional, defaults to 'null') :
    coverage packages (optional, defaults to 'null') :
    coverage ignored classes (optional, defaults to 'null') :

This will ask a few questions about the folders where your tests, source code and compiled files will be, and create a `.munit` and `test.hxml` files.
The `.munit` file will contain the folder configuration for our project. So assuming our project tree looks like the following:

    > MyAwesomeProject
    > |
    > |-- deploy
    > |-- report
    > |-- src
    > +-- test

The `.munit` file should look something like this:

```
version=2.0.0
src=test
bin=deploy
report=report
hxml=test.hxml
classPaths=src
```

We should now be able to run an example test, by executing the following command:

``` bash
$ haxelib run munit test
```

This of course is not very useful, but we can now start creating our own useful tests.
Let's assume we're going to create a test for our `Game` class in the `com.MyAwesomeCompany.MyAwesomeProject` package. I couldn't find a way to mirror the exact path name for the tests, as the package name would conflict with the unit under test package name. Because of this, I decided to use the following rule: Assuming our package and class names are named as in the example referred above, my test will be called `GameTest` inside a package called `com.MyAwesomeCompany.MyAwesomeProjectTest`
To create this test class, we can run:

``` bash
$ haxelib run munit ct com.MyAwesomeCompany.MyAwesomeProject.GameTest
```
This will create a test template that we can then edit and add our tests.

# NME and other libraries #

To be able to test Classes that use libraries such as `nme` or `actuate`, we need to add them to the configuration for every target on the `test.hxml`, as on the example below:

    ## Flash 10+
    -main TestMain
    -lib munit
    -lib hamcrest
    -lib nme
    -lib actuate
    -cp src

    -cp test
    -swf-version 10

    --next

You'll only need the `--next` on the last line if other configurations ensue.

# Some extras #

To make it easier to run the tests, I created a couple of aliases to be able to run tests faster. Because the flash target compiles faster than the CPP targets, I create an alias `munit` that runs the tests for the as3 target only.

``` bash
$ alias munit="haxelib run munit test -as3"
```

And an alias `munitall` that runs the tests for all the targets configured on the `test.hxml` file.

``` bash
$ alias munitall="haxelib run munit test"
```

We can add these to one of our shell configuration files so they're available at all times.

# Mocks and Matchers #

Unfortunately, due to poor documentation, I'm not completely sure if there's any mock support directly on `munit`, but from what I could see, it seems there's not. I found a few mock libraries, but couldn't yet decide wich one should I use, and how compatible they are with `munit`.
A similar situation happens with matchers, `munit` uses a `hamcrest` port for haxe, but I couldn't find any documentation on how to access these matchers or which ones does this port implement. Hopefully with time, as I write tests I'll find more about both mocks and matchers, at which time, I'll surely add a new post about it.
