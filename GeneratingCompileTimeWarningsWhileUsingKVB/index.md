---
layout: page
title: GeneratingCompileTimeWarningsWhileUsingKVB
---

KVB is really cool.  So is CoreData.  If you need to whip up a fully functioning protocol of something FAST (RAD) both technologies are a huge timesaver.  However, especially when using CoreData a lot of time savings comes from not having to write all your accessor methods.  That's great -- accessor methods are pretty straight forward (even when they are more than simple getters and setters) and as such CoreData is long pastdue.  What I'm having problems with is typos in my KVC keys, or changes in the type returned.  Short of having to write out actual accessor methods, what have you done to get warnings or errors at compile time?

Let me rephrase: what do you guys do to mitigate the step backward from type safety that KVC presents?

----

Test, test, and then test some more.

----

What's KVB? Never saw that one before  KVB is KVO+KVC  (KeyValueBinding).

Testing is always necessary but it is no substitute for a problem like this one in that code paths that are rarely reached are unlikely to be discovered as faulty until an awkward moment (i.e. after release or deployment).  At the very least don't do something stoopid like
      
[self willAccessValueForKey:@"myKey"];
blah = [self primitiveValueForKey:@"myKey"];
[self didAccessValueForKey:@"myKey"];

Use static strings or def'd strings so that *@"myKey"* is replaced with *MyKey*, which is either declared as 'static string MyKey = @"myKey";' or '#define MyKey @"myKey"'.

You're still stuck though with manually typecasting your returned values.
    
[(NSSomeClass *)[self valueForKey:MyKey] someMethod];


*Though you can     #define that, too :)*

    #define MyKeyType NSSomeClass*
[ (MyKeyType)[self valueForKey:MyKey] someMethod];


----

The argument between testing and compile-time checking is an ancient one which does not bear repeating here. Suffice it to say that when you lose compile-time checking, testing is how you make up for it, and the existence of large, working programs written in languages with little compile-time checking such as SmallTalk or Lisp indicate that it can be an adequate substitute.

*Even SmallTalk will warn you if you use a variable you haven't defined, n'eh? That's all we're looking to achieve: catching the stupidest errors at compile-time if it's no burden to do it that way.*

Absolutely!  Testing and compile-time checking is *not* an ancient argument.  There is an ongoing and very active movement to... umm... move to languages that are more safe -- not less safe.  Industry is moving away from languages like CPlusPlus and towards languages like CSharp (though I really wish it would have been something else) because CSharp is considered a safer language -- there are fewer things the coder can do to shoot himself in the foot.  With KVC and with CoreData and the Controller layer pushing us towards KVC, we are in some ways taking a step back and creating more opportunities for things to go wrong.  Typically you have a tradeoff between safety and performance (think C vs Java -- C is less safe but faster, Java is more safe but slower), but in this case we lose saftey AND we lose performance... I think Apple could have done better, and with a few hacks to the runtime unimplemented accessor methods could have been convereted to KVC calls, such that there is an actuall interface that provides us with some compile time type checking and interface validation.

----
Please don't let this page go cold without some answers... if nothing else, and if you KVC and CoreData and the lack of an interface (either code generated or runtime hacked by interface declarations without implementations) doesn't bother you say so.  If you have ideas, suggestions volonteer them.  But if this bugs you, incrementing a poll here would send a message to the great folks who brought us KVC in the first place.

----
There's always bugreport.apple.com. If we all file enhancement requests maybe we can get this in the next rev of XCode/IB.

