---
layout: page
title: DebuggingAFramework
---

Does anybody know how to use the debugger with a framework.  Suppose I have the following code

#import <MyFramework/MyFramework.h>
 
.
.
.

- (void)someMethod
{

   MyFrameWorkObject *object = [[MyFrameWorkObject alloc] init];

   [object frameWorkCall] // I want to step inside this call and actually move through my framework project with the debugger

}

----

Quoted from "Cocoa Programming, Anguish/Buck/Yacktman"

"Debugging frameworks is an extremely difficult task. The integration between the gdb debugger and PB's capability to display a breakpoint's source code doesn't work as seamlessly with frameworks"

Scott basically suggests what I usually do when creating a framework. I make a generic Cocoa application project that imports all the source that makes up a framework and debug the code with the app. This approach has a huge benifit of forcing you not to generate spaghetti code. Your framework should never be dependent on a particular interface. A framework should be a stand alone resource that can be easily shared between apps. Once you get your classes to behave, then you can think about building a framework. --zootbobbalu

----

It's possible -- I've seen it done at a job where I no longer work... and obiviously don't feel comfortable popping up out of the blue with "tech support" questions.  What you're describing, zootbobbalu, is good programming practice, though...  and often goes under the guise of unit testing :)

----

I guess there's a name for everything!!

http://www.stepwise.com/Articles/Technical/2002-06-17.01.html

--zootbobbalu

----

Again, I answered my own question.  It really doesn't make sense not to be able to debug a framework if you have the source.  All you need to do from PB is open up the framework project, open of the executable project, set breakpoints in the framework code, and run the exec under GDB from PB; voila!

