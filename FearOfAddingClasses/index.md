---
layout: page
title: FearOfAddingClasses
---

The fear of adding classes is something that can happen to a programmer when he or she perhaps doesn't remember the full value of ReFactoring mercilessly. My recommendation for moving on from this fear is to take a deep breath, archive a complete copy of the project's folder, and then go headlong into it, adding classes as needed towards a specific, achievable goal, like adding a feature or cleaning your code.

Remember, complexity in a project when it comes to the number of classes can be an indicator of *good* code design, or so says the LotsOfTinyClasses methodology.

----

Before going headlong, think about test-first development and what may need improving in the current tests.

If your fear is fear of not knowing how to solve the problem, trying writing a few scenarios describing simple cases of the problem and make sure you can satisfy them first. Try to write tests for your scenarios - if you can't isolate something testable then maybe your scenario is implausible. For GUI systems where you can't easily write UnitTest**'s, write "wetware unit tests" - walkthrough scripts for a person to test.

-- AndyDent 18th Aug 2003

----

Ah, yes... TestDrivenDevelopment (part of the ExtremeProgramming methodology) is indeed a good thing. By "going headlong" I was pretty much just describing my own system: figure out the core of what I need and spike it out. I test my code, just not with UnitTest<nowiki/>s... but anyhow, yes, for more on unit testing, one might look at OCUnit or ObjCUnit.

----

Why is having a bloated object graph good code design? One should have just enough objects to get the job one correctly, not one more and not one less. -- MikeTrent

----

There's no such thing as just enough objects. Any design can be modified to have the same functionality with less or more classes. It's not the number of classes that matters, it's the proper division of responsibilities. That's what's nice about unit tests and ReFactoring. If you don't have the proper division of responsibilities, you can try to get there by adding or removing classes without fear.

----

Quite. And who said anything about bloated, anyhow? We're not talking about adding classes willy-nilly until your problems are solved magically-- programming is still a more-or-less rational process. *Programming is the science of solving problems using just one more layer of indirection.*

----

After many years of OO I have reached a style where I try to use as few classes as are possible.

This is to reduce the number of dependencies and thus make CodeReUse and ReFactoring easier for myself.

Often I have found that OO is actually not the best tool for easy CodeReUse, because with OO often comes "interfaces" and "systems" (ObserverPattern, VisitorPattern etc.) which all require their own set of classes.

I read somewhere that out of the 21 or so DesignPatterns in the book by the GangOfFour, only 3 or so did actually require code in LISP (contrary to most OO languages, where each design pattern require a lot of code).  I might have the numbers completely wrong, but the point is still interesting (that with OO comes a lot of redundant code :) )!

*I don't understand: what required code in LISP? Surely not the patterns? Or were only three possible to code in LISP? The comment makes no sense, what has LISP to do with OO and 'a lot of code'?*

Did you re-read the paragraph before formulating this response? Do you know the book DesignPatterns? Do you know the language LISP? Do you know what OO is? It's hard to clarify the comment when all you say is that it makes no sense, and ask what required code in LISP when I actually wrote that most of the patterns did *not* require code in LISP.

From this page http://tinyurl.com/4tqzj is sort of the point I tried to communicate: *Some patterns are actually an artifact of the limitations of the language used. In highly expressive languages like LISP or Python they tend to just disappear*

An example could be the IteratorPattern, in ObjectiveC we write:
    
id obj;
NSEnumerator* enumerator = [someContainer objectEnumerator];
while(obj = [enumerator nextObject])
   NSLog(@"%@", obj);


If that is not a verbose way to     map     print onto     someContainer, I don't know what is... and with the above comes a) the container class, b) the enumerator class and, c) the container's enumerator subclass.

----

I find there is a balancing point between too many and too few classes. You can throw all your code into one class, but that becomes a very big mess. It becomes an even bigger mess if you randomly split up the code into a bunch of small classes which are tightly integrated with each other.

There is a technique which really helps me design and split up responsibilities: Instead of asking how many classes should I have, try to look at the number of classes each class relates to. An easy way to check is to see how many header files the class imports. Try to reduce this number - which reduces the number of relations - which simplifies the entire program.

Of course, if there's one big class then there is no need to import any other classes, but think of it as a percentage: one big class includes 100% of the system. If you find the percentage is too big, then you need to focus on refactoring that class.

If one small class only imports a couple other small classes, it may be only 5% or less of the entire system, so it is sheltered from all other changes made outside of these few classes. This is one great benefit of OOP. Exercising it to the full potential can create a very flexible system.

I just had an idea, and it's a good exercise for the reader. Write a script which calculates the percentage each class imports/uses of the entire system. You'll need to do some parsing of #import, but I think it'd be worth it. Don't calculate the frameworks, just the custom classes in your project. Anyway, here's the equation for each class:

    
(self_lines + imported_lines) / total_lines * 100


I think I'll go write my own now. -- RyanBates

----

This is one thing I really like about Objective-C -- sometimes a class just gets big, but by using categories I can split that class into several source files, thus making it easier to understand. For example, in a recent document class I split off the tool bar functions.

Mel Walker

