---
layout: page
title: CommunicationWithIB
---



Got myself into a bit of a pickle!

I've create a small Cocoa app based on an Apple template.
This template puts all the code in the 'main' file without the use of any classes.

All is working well, but i'm at a stage where i would like to output one of my NSString instances to a text field in IB.
When the newly created Controller class makes reference to an object created within the 'main' file, I am being told I am using an undeclared Function.

Am I going about this the wrong way?

many thanks, paul.

----

When you say "in the 'main' file", what exactly do you mean? Which template did you start from? Perhaps you could post some of your code?

*I assume he means main.m-- create a new Cocoa Application project, and take a look at how the main method calls NSApplicationMain to begin the run loop. Then, move your code out of main to the controller class (or others). In an object-oriented application, you don't want to have any of your code in the main method. Remember, unlike procedural programming, your controller class will execute its init method when it runs, and after that it's all event driven, unless you decide otherwise. If you still have trouble with undeclared functions, remember that you need to import the header file of each class you're using, in the .m file that uses them. -DF*

Many thanks DF,
Yep all code is currently in main.m.  Is it just a case of copying the whole code into a new class method to be called by the contoller?  I've been reading 'Programming in Objective-C' which implies the use of classes within the main.m - where would i put the code that is  repeated by the RunLoop? cheers.

*You can write small programs (object oriented or not) in main.m, but unless you're writing small throwaway programs this is considered bad design. Instead, you want to put the code into appropriate methods, in the appropriate class, just like you said. Once you work deeper with all of Cocoa's classes, this type of design will become commonplace to you. You usually don't interact with the run loop in Cocoa, although in some situations (such as game design) you might want to create your own loop. Instead, your application should be event driven (the user presses a button or sends a command, and a method is run), or you can set up an NSTimer in your -init method that calls your code every n seconds, and won't block any AppKit stuff that occurs behind the scenes. - DF*

Thanks DF, your comments have really helped.  I'm going to attempt splitting the 'main.m' code into relevant classes.  I'm trying to create an app that responds to MIDI information so it needs to constantly scan for Midi Messages.  This was achieved in the 'main.m' code with a CFRunLoop, but i'll give the NSTimer a go. :)

