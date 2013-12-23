---
layout: page
title: ObjectAlloc
---



A tool from AppleComputer that allows you to trace allocation and de-allocation of objects in your application, helping you to pin down MemoryLeaks.

----

Thanks to Eric Peyton who helped me figure out how to get started with ObjectAlloc:

In earlier versions of ObjectAlloc, you were not supposed to pick the app-bundle. (You **couldn't** pick it anyway...) Instead you had to go inside the bundle and run "Contents/MacOS/<App binary>".

Chris Kane from Apple explains the reasoning behind this:

"I leave you to choose the binary yourself because otherwise there's no way to choose other executables that might be embedded in your .app.  People put executables in their Resources folder sometimes, as an example, for tools and helper utilities and the like.

"Other developer apps have you choose the .app, and then pick the app executable for you.  To go into a .app wrapper to get at some other binaries involves some hard-to-discover contortions.

"Really, there should be a switch at the bottom of "open" panels in developer tools which allows you to toggle "go into bundles" mode."

As of the Jaguar Development tools, that "button" exists - although it turned out to be a checkbox.

----

**How to use ObjectAlloc**


* In the Run... panel chose the switch at the bottom to see all events.
* Run the app and get it good and warmed up; switch back and forth 
between OA and your app a few times, too.
* Choose the "Show since mark" switch at the top of the window.
* Click the mark button to set the mark.
* Go back to your app and perform the action tha causes the memory leak 5 times.
* Click on the column title for Current in ObjectAlloc to sort the 
still-allocated objects to the top.  If there's a leak for every time you performed the action, there should be 5 objects still allocated.  If there are more,  things may not be warmed up enough.  Go back to the previous step.  However, if 
there are more, it could mean there are multiple possible leaks in 
different places, or other factors.
* Pause the data collection with the pause button in OA.
* Switch to the middle tab, choose the type of the leaked object in the left column.  The 
middle column will show the currently allocated instances (since the 
mark) as pointers.  The events associated with that object are in the 
third column.  If you choose an event in the third column, the event 
inspector will move to show that event.  Now, the retain and release 
events need to match up with sensible backtraces, and each autorelease 
should have a subsequent release from an autorelease pool (but the stack 
will be much different there).  There should be an extra release for the 
object allocation, and finally a deallocate event (though since the 
object is still around those won't have happened).
* Here's where recipes break down and you sort of need to deduce 
things based on what you see.


(Based on a Mailing list post by Chris Kane)

----

** Some other hints and tips with ObjectAlloc **

Memory leaks have a distinct tendency to appear within your own classes - When you have a leaky app start at the highest level objects first.  So, if you have a custom class 'A' which contains objects of custom class 'B' which contain objects of custom class 'C', track down the 'A' leaks first.  Sounds obvious, but you'd be surprised how often code blindness sets in.

Other prime candidates are the collection classes.  Leak one collection and you may well leak tens or even hundreds of other objects as a result.  Again, go for the top level first.

** So: ** It can be useful to sort by number of allocated objects within ObjectAlloc, then work up from the bottom of the list. In a recent example, I had an app that was leaking hundreds of Kilobytes in thousands of objects per iteration - all due to _one_ NSArray being retained twice.

Other things I've had to (repeatedly) discover:

Remember to autorelease your objects if you're giving ownership to another class.  This applies particularly when you're adding AtomicObjects into collections. If you've allocated e.g. an NSString and then want to put it into an array and forget about it (no longer having a pointer to it), then you must release it.  It's important to remember to do this because even though each individual leak is small (and therefore, potentially hard to notice) the cumulative effect of lots of them can be quite large.


Don't forget to release objects you own on dealloc.

