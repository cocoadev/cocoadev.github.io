---
layout: page
title: OptimizeNow
---

When is the right time to optimize? Well...


* are you still adding major features?
* is your design sound?
* is your product too slow right now?


----
I'd like a bit of clarification on the 3rd point. Your product can be too slow after only 10% of the development work has been done. Unless you're in the gaming industry - or other situation where resources/computational cycles are known to be critical before the app is built, just because your product is too slow is no indicator that it should be optimized now.

*Agreed. You should change the item in the list, perhaps.*

**Second thought: "too slow" carries some context with it. How slow is too slow? That varies from project to project (and task to task!).**

Martin Fowler's Refactoring book has an excellent excerpt from Rich Garzaniti - a member of Chrysler's C3 project team. The short version - processing payrol went from an estimated 1,000 hours down to 3. There is no statement in the excerpt regarding how/when they chose to optimize. (i.e., how far along the project  was, etc ).

http://www.amazon.com/exec/obidos/ASIN/0201485672/002-1805203-7586458

----
On the Topic of when to optimize, I thought you all might find it interesting to know that during the development of Safari, Apple forced engineers to optimize up-front and refused to let them release a version that was any slower than a previous version... ehh?

Maybe that's why Safari lags in support for css and other standards?

*In the case of Safari, speed is one of their most important features.*

So it's ok to be broken, as long as it's fast? Or, in other words, it was optimized before it was correct?

*Decide for yourself. Personally, I love love love Safari, and its CSS support, and all the rest.*

**I think it's a matter of priority. For Safari, they considered speed to be more important than simple, clean code. A few other projects such as games might fit in this category. However, for most projects the disire is clean and simple code that you can OptimizeLater. Speed should be very important to you, but think of it this way: It's easier to optimize clean code than it is to clean up (refactor) optimized code - in fact, you will probably end up re-optimizing it after you refactor.**

----

Another factor to remember is that programmers have (at least) two different ways to measure "fast".  A loop can be slow and not affect performance as a whole, or performance overall can be slow.  In general, the first one doesn't matter to anyone but the programmer, because although it's inelegant, it doesn't really do anything bad.  The second matters to everyone who uses the thing.  The OptimizeLater crowd is of the mind that you can more effectively fix the second case by waiting until you can actually tell what's causing it in practice, rather than attempting to optimize every loop or transaction you think isn't quite perfect enough.

That brings up the important point that programmers are **really bad** at knowing where the performance problems are.  Often times I've come into a client situation where they say "our file reading code is slow - tweak it so the reads are faster", and the actual problem wasn't there at all but in the building of the data structures from the file.  The actual file read was very fast.  Another client 'just knew' their performance problems were in a tree layout algorith.  By actual profiling, the real problem was in a routine that constructed time and date stamp strings for display in the tree.  The pessimal C++ code was literally constructing and destructing dozens of objects per string, but none of the programmers thought to look there.

*"PessimalWord" is one of my favorite terms.*

----

I have to say that one thing which really solidified my views on this (I'm in the OptimizeLater crowd) was something similar to the above poster. In trying to debug some performance problems in an app, I tried lots of different strategies, and none worked. It was only later, much further into the app, that I discovered Shark, and needless to say, the problem was nothing like what I thought it was. I'm actually willing to say that it's more *fun* to optimize afterwards anyway (in addition to the practical reasons), because you get such great smug satisfaction from benchmarking a routine, optimizing it, and getting a great performance boost.

