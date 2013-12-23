---
layout: page
title: OnceAndOnlyOnce
---

"The philosophy that information on how a piece of software operates - be it an algorithm, a set of constants, human-readable documentation, or something else - should exist in only one place. The practice of this is not always easy; in most languages (CommonLisp advocates claim that practicing OAOO is significantly easier for them), there are many types of situations where there is no obvious way to follow OnceAndOnlyOnce, or where following it requires more effort than simple duplication. The essence of it, however, is: Write everything once and only once - locate and fold together any duplication you find while adding the current feature. 

Practitioners of OnceAndOnlyOnce believe that, if information is written down in more than one place: 

*One of the versions will soon be out of date, since it can be very difficult to remember to update them all ("If the code and the comments disagree, then both are probably wrong." -- attributed to Norm Schryer) 
*They will have more to overcome when they want to change directions (See: RedundancyIsInertia) 
*The responsibilities become scattered, leading to code that is difficult to understand. (See: OneResponsibilityRule) 


 Applied strictly, this even means that documentation and source code should complement each other, not provide information obtainable by looking at the other."

Quoted from http://c2.com/cgi/wiki?OnceAndOnlyOnce.
See ExtremeProgramming.

I decided I couldn't say it any better than the WikiMasters at c2...

