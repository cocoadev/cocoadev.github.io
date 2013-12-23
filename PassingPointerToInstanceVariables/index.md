---
layout: page
title: PassingPointerToInstanceVariables
---

I was adding a book recommendation to the "other books" section when I found myself arguing with my own description to the book. The book I am recommending is a book for learning C, "The C Programming Language" by Kernighan and Ritchie. I'm sure many people know about this book, but I feel the book cannot be recommended enough.  The description I gave is as follows:

A must read for anyone who wants to learn a language built on top of C. If you don't know who Kernighan is then you should find out!! Apple's book on Objective-C "Inside Cocoa: Object-Oriented Programming and the Objective-C Language" recommends one book, this one (Chapter 1 p13). Need I say more.

Yes, I agree this is a supplemental reading.
Yes, I know this book is for pointer loving bit flipping freaks.
Yes, I know Cocoa is a higher level language and a much more elegant way to program.

But.......

After drinking the Kool Aid and pledging my elegance to OOP, I have to admit that I grab this book as much as I grab any other. I'm not in any way saying that procedural programming is the way to go. Cocoa is where it's at!! I do think that this book still needs to be recommended. Apple will even fall back on simple C function calls when speed is critical. 

    

void NSFrameRect(NSRect aRect);
void NSFrameRectFillList(const NSRect *rects, int count);



IMHO encapsulating to the point of rarely passing pointers is a bad habit to get into. Setters and Getters make for clean interfaces, but don't kill yourself over it. Almost all Cocoa programming books ignore the issue of how to mix C code with Objective C code. Many Cocoa books will make a passing statement like "Objective-C is a superset of C so you can do things the C way if you want to", but Cocoa book authors are so consumed with preaching the ways of OOP they forget to explain how to hack for efficiency. 

Yes, unexpected things will happen when you stop encapsulating all of you data. Yes, objects that are not encapsulated should not be considered true objects. Yes, hacking for efficiency creates bugs that are hard to trace and makes your code less self documenting. 

But.........

If done carefully and with discretion, passing pointers to instance variables is a practice that has a place in Cocoa programming. Wrapping objects that share data into a single autonomous object should be a design apprach to consider. 

any thoughts?

----

Like a lot of stuff in programming, it takes experience and judgment (and making lots of mistakes :-) when deciding where to break the encapsulation barrier.  The BigNerdRanch folks told us to Prefer objects, but when things make more sense as structs, make them structs.  particularly short-lived stuff (like NSRanges), or things that are small and lightweight (like NSRects. adding an extra 4 bytes to an NSRect for the Objective-C isa pointer is significant.  An extra 4 bytes to an NSTextView for instance isn't significant)

as you noticed in Apple's/NeXT's stuff, they pass these small structs by value.  The extra bytes wasted on the stack is made up for in time not spent dereferencing pointers to the structures.

When to go 'low level' in  your own clases, by exposing implementation details directly (vs hiding them in setters and getters) is a design choice.  In general I like to postpone optimizations like those until I've had a chance to measure where the problems actually are. You can cause yourself more headache in maintenance by exposing details than you save in run-time.

----

Make it work, *then* optimise it.

*But remember, sometimes using a struct is the easiest way of doing things as well as the cheapest. NSRect would make no sense as an object.*

----

On topic: anything's fair as long as you document the behavior and follow the rules. For example, I might write an object that returns a reference to an ivar. That reference might be a pointer to a char *, or maybe even a pointer to another Objective C object. In my header file I list the rules ("the caller must not free the pointer..." or whatever). 

Off topic: I can't imagine life without the K&R. It's perhaps the best programming language book ever written. But then again, C is probably my favorite programming language. I think that's why I like Objective C a lot more than the leading brands (C++, Java). 

-- MikeTrent

