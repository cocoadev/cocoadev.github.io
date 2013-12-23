---
layout: page
title: AutoRunCode
---

Other than -init, -awakeFromNib, -dealloc, etc. are there any methods that run "automatically"?  In the examples I've found and trying to follow, most of their GUI apps have you press a button, which calls a method, which does something.
 
Well, I'd like to do that "something" automatically.  For instance, after calling awakeFromNib, setting variables and such, I'd like to be able to have a method run w/out having to explicitely call it from elsewhere.  Is this possible?
 
Say, for instance, I was to have a preference to start a task automatically when the app launches, rather than, say, pressing a button to initiate the task.  This make sense? :)

----

NSApplication does have the following (among others) delegate method     - (void)applicationDidFinishLaunching:(NSNotification *)aNotification

You can also register for this notification (NSApplicationDidFinishLaunchingNotification) explicitly in other classes, if you want it.

----
 
Now, see, I thought I tried that, but it didn't work.
I have an outlet, which I called outPut declared in the header file
If I do a:   [outPut insertText:@"This is a test."]  in the -awakeFromNib method, it displays correctly
 
But I try to put it into the - (void)applicationDidFinishLaunching, and I don't see anything.  So I figured I was using the wrong method, or something.

----

Was the applicationDidFinishLaunching method in your application's delegate object? Or did you register for the notification?

*Sounds to me like outPut wasn't set at the time of -applicationDidFinishLaunching. Check to see if it's nil with a simple      if(outPut) NSLog(@"bah");  statement, and let us know.**

----

If those approaches don't work, you could always use a one-shot NSTimer. It just has to run once and then remove itself.

E.g. I have one app that displays a splash screen at startup. The screen fades in using an NSTimer, then the timer sets its next fire time to "in three seconds" so the user sees the splash, then fades out again. The overall running time isn't even three seconds, but on slower Macs, documents the user opens or loading of plugins at startup happens while the splash is up, which makes for a much more responsive app. -- UliKusterer

----

Just a style note - <code>outputView</code> or something would be a better name for the outlet. 'outPut' just looks really odd. See [http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingBasics.html]

----

    +load and     +initialize are called automagically by the runtime before loading a class (    +load) and before a class is instanitated the first time (    +initialize). See http://gcc.gnu.org/onlinedocs/gcc-3.2.3/gcc/What-you-can-and-what-you-cannot-do-in--load.html for more info.

----

If you want to do something whenever something happens in the run loop, you can use     CFRunLoopAddObserver() to add a callback as an observer of the run loop.

