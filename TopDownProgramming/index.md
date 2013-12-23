---
layout: page
title: TopDownProgramming
---



A development approach wherein the highest level modules of a solution are written first and initially only call *stub* routines which are to be filled in later.

Not surprisingly, this is the opposite of General/BottomUpProgramming.

Pros:

* Work is easily divided amongst a team because all the interactions of the problem's subcomponents are well-defined in the first phase of development


Cons:

* Code may be duplicated between the subcomponents or subsubcomponents may be made too specific to the task required for their parent subcomponent.  This problem can be reduced by keeping a close eye on which code can be tweaked or re-factored to make a bunch of special components more generic


General/TopDownProgramming is one approach to General/ExtremeProgramming since code is only written once something needs it.

Fill in any other ideas that come to mind. Most of the developers I know shy away from these terms since they are frequently confused so it would be good if we can nail down some concrete terms, here.

--General/JeffDisher

----

It seems like this is almost a synonym of spiking; you start at the top and work downwards in a narrow bit of the program, and then widen out the spike as you go. I am *very* fond of writing code only when you've already referenced it; in this way, the client code almost becomes a General/UnitTest. (For reference, I've not yet been the good sort what does proper unit testing. I've been the bad sort what keeps looking at it and shying away.) In any case, code's semantics are in its use, so I don't find it much use to flesh out too much of an object's interface before I know who'll be using it-- because projects evolve to fill their problem space, because (in my opinion) the only way to know a problem space well is to fill it with a solution.

-- General/RobRix

----

Needless to say, those terms are here to stay. Still, whoever invented them was much more heavily-influenced by the hierarchy of programming
*teams*, rather than that of the software itself.

Much more appropriate would have been the term "inside - out" programming (with its 'opposite', "outside - in").

Object-based, and even procedural programs do not really have a top and bottom, since both of them are systems
in which elements subsume but do not necessarily supersede others in importance.

Beware of the way the language you use enforces social, rather than technical, constraints.

For instance, would you rather be a worker ant laboring at the *bottom* of a program hierarchy, or an architect working at the *top*?

Consider the topsy-turvy social order when the worker ants are *insiders* and the architects are *outsiders*. What manager would stand for that?

Long live the Revolution.   ;-P

----

How about "Wag The Dog" programming? ;-)

*Isn't that what Pavlov was doing?*

----

The idea of top and bottom comes from how close it is to the hardware (the bottom - since you build things on top of it) vs how close it is to the conceptual meaning of the problem (the top - since it is what you grow towards).

Lower-level code refers to things like hand-coded asm or, by modern standards, pure C while high-level code refers to functional languages and OO languages.  These are common terms and the top-down and bottom-up terms are an extension of this idea.

--General/JeffDisher

----

Bravo. Well explained. And you get extra props for using the word "conceptual" when you very easily could have gone with "canonical"...
