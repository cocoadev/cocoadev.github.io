---
layout: page
title: AspectOnSketch
---

**AspectOnSketch** - an example usage of AspectCocoa

The Example application Sketch that comes with Apple's Dev tools has undo functionality scaterred throughout...
We will attempt to use Aspects to centralize this functionality into one location.

First we will create a class, instances of which will be used at AdviceObjects

    
//code


Next, we will define our PointCuts and the undo Aspect.. we'll do it in that class we just defined for sake of convenience..

    
//code


Finally, we will have to add into our code somewhere the decleration and creation of this Aspect...(we'll use main.m so that our Aspect loading code is guaranteed to be the first thing that executes)  And we'll remove(or comment out) all the old undo code.

    
//code

