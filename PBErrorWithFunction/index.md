---
layout: page
title: PBErrorWithFunction
---

I have a method, and then a function:

    -(void)update;
-(NSNumber *)maxPopulation;

maxPopulation takes no arguments - it uses instance variables to determine the value to return. But whenever I call maxPopulation in update, PB says: 'maxPopulation' undeclared (first use in this function). Any ideas about what I'm missing?

----

Looks good to me.  Let me see the code of the maxPopulation in the .m file.

*Better show us what project headers you're including, as well.*

----

You have declared maxPopulation as an instance method of whatever class you are working on.  It is not a C function.  To call it you need to send a message to a class.  So in update you probably want to use something like NSNumber *max = [self maxPopulation];

----

Yep, that's what it is.  When you declare something as an instance method, you can't simply type something like

    
myNumber = maxPopulation;


Rather, you have to send it as a message to an object - say

    
myNumber = [self maxPopulation];

