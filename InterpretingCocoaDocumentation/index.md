---
layout: page
title: InterpretingCocoaDocumentation
---

I'm not a comp sci major, I'm actually network administration so I don't have much programming experience, however, I've always been interested in development in some way shape or form and ever since I've made the "switch" I have been extremely fascinated with Cocoa.  Next semester I'm taking a independent study in Cocoa development, the course will totally be up to me and I'm hoping to make the best of it.  I'll be using the infamous Hillegass book.

When working with Java I found the documentation to be a great resource (duh) and I hope to use the Apple Cocoa documentation a lot throughout the semester.  I might be jumping the gun but reviewing the documentation for Obj-C I can't quite put it together.  Does anyone have a good explanation on utilizing the Apple documentation or how to read it?

Example using the NSTimer class

Creating a timer
+ scheduledTimerWithTimeInterval:invocation:repeats:
Returns a new NSTimer object and adds it to the current NSRunLoop in the default mode.

Information about a timer
� isValid
Returns YES if the receiver is currently valid, NO otherwise.

What are the purposes of the "+" and "-"?  I'm guessing I'm just jumping the gun on the language but any information would be appreciated! 

Thanks!

----

+ means it's a class method, - means it's an instance method.  I'd recommend reading Apple's online book, The Objective-C Programming Language before diving into Cocoa.  Do you have to?  No.  But it isn't that long, and if you do that then you'll have a thorough understanding of the underpinnings.  Then you can start slapping piecemeal Cocoa knowledge on top. :-) http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/index.html


----

Before reading the docs, read Aaron or equivalent. Then ask questions ;-)

