---
layout: page
title: PointCuts
---

**PointCuts** - 

a JoinPoint is a term used when refering to aspects to define when (amongst the mass of messages being sent from object to object) we would like to intervene and do something else.  
a PointCut defines one or more Join Points.  Now if a PointCut defined just one point in code, then we wouldn't need to use aspects or anything.. we'd just add something in to that one point in code.  But if a PointCut is a more abstract desciption of when to "do something else" or should be insterted into code we don't have ourselves... then we'd like some help from some sort of Aspect such as those descibed in AspectCocoa.  In AspectCocoa all PointCuts result in the creation of method wrappers, and so in the case of AspectCocoa a JoinPoint can be summarized to mean "some method call".  In AspectCocoa, a JoinPoint is any method call that is contained within the scope defined by the PointCut.

