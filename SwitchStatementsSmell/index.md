---
layout: page
title: SwitchStatementsSmell
---

Just a note from my current project - I'm sure someone can fill this topic in more comprehensively.

A common Cocoa use of switch statements is in menu selections. Something like:
    
-(General/IBAction)menuAction:(id)sender
 {
    switch ([sender tag])
    {
        case 1:
        [someObject doSomething];
        break;
        case 2:
        [someOtherObject doSomething];
        break;
        ...
    }
}


You can replace this by setting your menu items' representedObject:
    
-(General/IBAction)menuAction:(id)sender
 {
   General/sender representedObject] doSomething];
 }


----

Although I do not think that this is common in Cocoa, should it be, then I think a better replacement would be to set the object as the target of the menu item (so that the action message is sent directly to the object).

Generally though, you want to leave the target blank, and have the responding object in the responder chain, which also gives you automatic menu item enabling -- give your menu items different actions, not tag values. I think tag values are sort of an extra argument, e.g. if you have a sub menu to set the tab size, you may have some pre-defined values (3, 4, 8) and you set this value as the tag value, and the action method to takeTabSizeFromMenuItem: or similar.

I think the [[SwitchStatementsSmell actually refer to code like this:
    
switch([someShapeObject typeID])
{
   case ShapeTypeID_Rectangle:
      ...
   break;

   case ShapeTypeID_Circle:
      ...
   break;

   ...
}


This is how people would do things 10 years ago, when OO wasn't broadly accepted -- but today we all know that the code should instead be:
    
[someShapeObject performSomeShapeAction];


----
In the cases where polymorphism makes sense, I agree with you. But it should not be misused either. In many cases, switch statements don't smell and with modern compilers routinely transforming them into jump tables, they offer far superior performance.
----
*Performance should not really matter -- the idea is separation of concerns, i.e. the object iterating the shapes is only concerned about dispatching the messages, not how they are actually handled -- but yes, switch cases have many well-justified uses, saying that General/SwitchStatementsSmell is also very misleading, and I think it stems from how old Cobol programmers were using them to switch on type of object, but I personally have never really seen them misused this way.*
----
*Unfortunately, in the real world outside of academia, performance does really mater. The idea of separation of concerns is nothing new with XP; This is just good generic programming practice.*
----
Performance also matters in the academic world -- but true that the main measure of performance is time complexity and not wether a jump table created by the use of switch/case might be faster than dynamic dispatch thru polymorphism (granted they both implement the thing in constant time) -- however, the academic world is concerned about optimizing both of these techniques independently.

But this is not why I said that *performance should not really matter* -- I said it because most likely the code in question is not the bottleneck of the program and which approach is the faster may depend a lot on the compiler, so choosing between the two based on what the programmer may think about performance is IMHO outright stupid! Especially because the choice will affect a lot of the code, and wether or not one is faster than the other may change with the compiler and may even change over time when the source grows -- e.g. the switch may no longer be implemented using a jump table if values are not consecutive, or the method dispatch may be able to inline the code if the type can be deduced compile-time or in the case of General/ObjectiveCee, the class may get a lot of new methods which cause clashes in the method caching etc. etc.
----
The statement

*most likely the code in question is not the bottleneck*

is a generalization and an assumption.

The statement,

*so choosing between the two based on what the programmer may think about performance is IMHO outright stupid*

Does not jive with the real world. When a working developer writes code based on what he/she **Knows** not **Thinks**, and yes, we **Know** what our compilers are capable of, he/she will tailor code that is optimal for that compiler. If you think different, you are very much mistaken. Are you calling us all stupid?
----
Calling a decision stupid is not the same as saying that the person did it is stupid -- I do stupid things myself, but do not generally consider myself stupid! :) But maybe I should have used the word *naive* so to not offend anyone, or perhaps *under-informed*, as that is probably the most diplomatic phrasing.

I do not see the purpose of pointing out that my *most likely* is an assumption, of course it is. And this page talkes about using switch statements in general. Not about some very specific case.

And even though there are a few programmers who do know a lot of internals about their compiler, these are really a rare breed, and even with this knowledge, I doubt they can make a decision about wether switch/case is faster or slower than e.g. a virtual member function (in context) -- especially since this does not alone depend on the compiler, but also on the architecture, for example the G5 has a much longer pipeline and relies heavily on branch-prediction, so a carefully crafted if-then-else list may perform significantly better than a jump table, whereas in the old days, there were no doubt about the advantages of the jump table.

Let me stop by saying that a lot of developers **think** they **know** the performance characteristics of their environment, but there is a lot of literature to counter their belief -- but naturally there are also exceptions who truly know the ins and outs of both the compiler, architecture, and how these things play together, when certain optimizations will and will not be performed by the compiler etc. But these people are most likely not the same who will need to go look at General/CocoaDev to see why General/SwitchStatementsSmell :)

*Additionally their knowledge may quickly get outdated, e.g. a year ago we were using GCC 2.98, today we use GCC 3.3 where a lot have changed, and in a year from now we probably use IBM's compiler -- the underlying architecture has also changed from Motorola's G4 to IBM's G5 -- this is why it is much more valuable to learn about how to create good designs/code maintainability and theoretic performance measurements like time complexity (General/BigONotation) -- but of course, if you work with a given compiler for a given embedded system or similar, it might also be very necessary for you to learn about the practical aspects -- but the performance problems I generally see in software is most often scalability, i.e. the program did fine when it was written, but a year later where data has grown to many times of what the developer tested it with, it no longer does, and this problem stems from a lack of theoretic knowledge, not practical, at least this is my claim!*
----

Recently I have been influenced by General/GenericProgramming, and I have started to doubt the sanity in the approach above.

Let us use another example -- given a mouse click, we wish to figure out which window contain that click. The OO approach would have us do this:
    
General/NSWindow* window;
General/NSEnumerator* enumerator = [allWindows depthSortedEnumerator];
do {
   window = [enumerator nextObject];
} while(window && [window containsPoint:thePoint] == NO);

// use window if != nil


And then we would have to implement containsPoint: for our window class. The advantage is that if we decide to introduce round windows, windows with transparent areas, or similar, then we can just overload the method for these special cases, and the original window-finding code will still work.

The downside is that none of this can really be re-used. For example, when we have found the window which contain the point, then we also wish to find the view which contain the point, and for this, we need to write the same loop as above, and add an implementation of containsPoint: to our view class.

Regarding the containsPoint: implementation, then we may be able to use inheritance to avoid it, but then imagine that the view which does contain the point, is an General/NSForm, General/NSMatrix, General/NSTableView, or similar, i.e. a view which layout several cells, now that view once again needs to do exactly the same, and if we do not wish to have 3 implementations of containsPoint:, then windows, views, and cells need to have some common super class which implement the method, but this will quickly result in bloated super classes, cause there are so many other things where "algorithms" could benefit from the object implementing some helper method, and there may start to arise problems fitting these into a meaningful class hierarchy (multiple inheritance or mix-ins may help solve this problem, interfaces doesn't, because they only declare the method, they do not come with an implementation).

To sum it up, we have a re-occurring problem (find the first rectangle (of several), which contain a point), and it seems that we must implement the code to solve this problem each time -- and to do it "correct" we must add a new method to the classes involved (and generally we wish to keep the number of methods down).

Another problem is that if we start a new project, and the same problem arise, it is not easy to cut'n'paste code from our old project, because we split the actual algorithm into different pieces which most likely are not in the same compilation units.

Perhaps this is just the price one pays for flexibility? General/GenericProgramming generally works exclusively with homogenerous types, whereas e.g. a GUI relies heavily on polymorphism. So I am uncertain to how much a GUI could benefit from General/GenericProgramming.

I would be interested in any comments others might have on this issue.
----
When you embrace generic programming practices you should also embrace reuse. When you have a true generic algorithm you may want to consider placing it in a header or wrapping it in a library/framework in lieu of "cut and paste" for use in other projects. If you find that you can't achive this, then the algorithm is not truly generic, is it?

*Ideally -- but there are many practical concerns, for example if one project is shared on CVS with other developers, your only choice really is cut'n'paste. Also, you probably shouldn't place an algorithm in a shared header before you actually have several uses for this algorithm, since there is maintenance overhead by doing so, considerations about name clashes, and the chance that you end up with a zillion generic algorithms in one giant library, where each is only used in one or two projects.*
----
Quote: *for example if one project is shared on CVS with other developers, your only choice really is cut'n'paste*

Why would that be? That is the whole point of version control.
