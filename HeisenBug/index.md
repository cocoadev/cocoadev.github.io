---
layout: page
title: HeisenBug
---

[From Heisenberg's Uncertainty Principle in quantum physics] A bug that disappears or alters its behaviour when one attempts to probe or isolate it. (This usage is not even particularly fanciful; the use of a debugger sometimes alters a program's operating environment significantly enough that buggy code, such as that which relies on the values of uninitialised memory, behaves quite differently.) 

Some significant causes of heisenbugs:


* Uninitialised auto variables. One way of avoiding such problems by default is to use the C++ technique, "Allocation is Initialization": only declare your variables when you are first putting something in them. That way, when you rewrite the code, you can't accidentally remove the initialization without causing the compiler to choke (since the variable is no longer declared). You also prevent yourself forgetting to set up the variable when you first write the code.

    int i; // Bad
i = 5; // Try not to separate declaration from initialization
int i = 5; // Try to do this instead


This is not always possible; in the cases where it isn't, it may be helpful to comment the code a little to remind yourself to initialize the variable correctly should you refactor the code later.

* Fandango on core phenomena (especially lossage related to corruption of the malloc arena) or errors that smash the stack. Overrunning arrays, either by using extra-large indices or with bad pointer arithmetic, is a major source of bugs and even security vulnerabilities. The simplest way of avoiding them is to use C++ STL containers like     vector, avoiding unchecked methods, or ObjC containers like NSArray in less performance-critical areas of code. The alternative is, of course, to rewrite the wheel and produce slightly more verbose code.

* Multithreading problems. Multithreading is a great way to keep your application's UI responsive when processing data, but produces the most unbelievably hard-to-find HeisenBug<nowiki/>s. Take a look at ThreadSafety for some advice. The best approach to solving multithreaded heisenbugs is to not produce them in the first place, as locating one can take days!


See also: SchroedinBug, BohrBug. 

[Originally copied from: http://wombat.doc.ic.ac.uk/foldoc/foldoc.cgi?heisenbug]

**See also:**

* http://en.wikipedia.org/wiki/Heisenbug
* http://twoday.tuwien.ac.at/IMHO/stories/3320/


----
**Discussion:**
----

Just a quick attempt at fleshing out this page. Comments + suggestions welcome. (Rewrite the wheel was a joke, btw.)

**'Re-cast* would be good. Wheels being made of iron, in many cases. Just ironing out the details. With a little irony. Sorry about "steel"ing your thunder. Or testing your mettle.*

Okay, I submit! You've stolen my punder.

