---
layout: page
title: ReadableDesign
---



One thing that greatly aids in a ReadableDesign is proper naming of variables, methods, and classes. Names should not be too short with unusual abbreviations. Neither should they be too long and hard to read/type. They should concisely and clearly define the role the piece plays in the design.

Long methods are hideous and hard to read, therefore, keep them short by splitting long methods up into several smaller ones. If a piece of code is hard to understand, instead of writing a comment for it, place it inside a method with a name defining what it does. Even if the method is one line long - it doesn't matter. The only time a method is too short is if the contents describe what it does better then the name of the method.

Don't worry about performance until you have tested it and know it is too slow. Optimization will be easy to add afterwards because the design is easy to understand.

Large classes are equally hideous. Instead, stick with the single responsibility principle. If an object is doing too much, delegate the work on to several smaller objects.

Usually, creating a ReadableDesign leads to a more FlexibleDesign - they go hand in hand.


**Taking It Too Far**

It is hard to take ReadableDesign too far and very few people have a problem with it. One example of taking ReadableDesign too far is to have 100 single line methods. The result is, of course, more difficult to understand due to the shear number of methods. Balance it out with SimpleDesign, but don't let that hinder you from making a good ReadableDesign.

