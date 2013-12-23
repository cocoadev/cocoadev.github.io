---
layout: page
title: ProgrammingProblemsFun
---



This is a discussion where people discuss their problems that are funny.

----

I haven't found anything in programming to be funny, but just completely frustrating. I will spend hours on something stupid, then sleep on it, and wake up with the solution which usually is extremely simple. And I think, what a waste of time. Not funny at all. I only laugh when I see other people's programs and how much they suck and how much I can make a better product.

----

I find situations like that quite funny, as generally when I am coding late at night I am saying "I know when I wake up in the morning that I will have the answer, I should go to sleep". Of course I don't.

One of the most enjoyable coding problems was hunting through a mass of code for an error only to find it was an array that was declared that was not nil ended.

Oh, I guess there may be just funny code. For example, I happened to write this yesterday: [[self superview] superview] superview] close]. Silly as it may seem, when I saw it I laughed for half an hour.

* Wouldn't [[self window] close] suffice? (Unless it is something totally different) :)*

----

*Somewhere in a turtle graphics program there were two Boolean functions called [[PenIsUp() and PenIsDown().*  **Off-color "joke" aside, don't you mean LOGO?**

My most common error: not returning self in an init function.

*At least the compiler catches that.  My most annoying common error: not calling [super dealloc], not properly releasing an object created with an init method when not in an accessor.*
** --> New with Xcode 2.0 (probably new in gcc) : warning when you forget [super dealloc]!!!**

and there's an easy fix for the 2nd one: use the accessor.

*Cocoa memory management is so transparent that I forget to do it when I have to. :-/  I can hold onto doing it in accessors, but in the middle of a random function I tend to forget (why do some classes not have convenience creational methods???).*

Heh, Cocoa MM seems transparent to you?  I guess I'm spoiled by Lisp.  The rule applies to any common error: if it's a mistake made often enough to identify as such, the compiler should do it for you.

*it's not really that complicated... alloc,copy,retain = release*  **don't forget new too** *actually, i rather prefer to forget about it ;)*

----
Working on a programming-based problem for 15 minutes or more and finding out you can do it in IB easily. Or vice versa.

----
I hate it when I set a selector for a control action that is slightly misspelled ala     [ctrl setAction:@selector(performSomeActon:)]. Not a hard problem to fix, but my app takes 5+ minutes to build/link from scratch! *Buy a faster HD; get more RAM. :)* Not always an option. *Then use zero-link. Or better yet, don't misspell selectors!* don't spell them at all - use IB

----

My most common error is adding a crapload of methods to one class, then having to add a bunch of accessors so i can call them from the class they should be in to begin with.

Also adding methods at whatever location in my source file i happen to be scrolled to, instead of keeping them logically grouped.

I'm a slob!

----

Anccessor methods aide with subclassing as well, such is why I use them even in the same class (though only for boolean returns).

