---
layout: page
title: NewbieNeedsHelpWithTime
---

I'm very new to Cocoa, and indeed have only had basic experience with the easiest areas of Delphi, which at first glance at least is easier to grasp than the (seemingly?) more open ended/loose Cocoa/Objective-C stuff. 

First of all, can someone explain to me what is a 'Class' ?

Also, if you're feeling helpful, I would like to do something with timing in a little application. Could you let me know simply how to go about for example timing the number of seconds between the time you press one button, and the time you press the second? Then assign this value (of seconds) to an integer, and then take this integer and pop it in a textbox.

Many thanks for any help offered!

-Peter Laurens

----

First, you need to read up on ObjectOrientedProgramming. For a quick run down of objects and classes I think there is some info in AnObject. Then pick up a book on a Cocoa. For instance any of the CocoaBooks.

--GormanChristian

----

Also look through Apple's developer site for the PDF, **Object Oriented Programming And The Objective C Language**.

----

If  Time is of the essence, consult the documentation on NSDate. The message [ NSDate date ] returns the current moment. Of course, by the "time" you get that information, it will be "later", since signals travel no faster than the speed of light in a vacuum (and somewhat slower inside your  machine.) In material media, though light can travel faster there than it does in a vacuum, there are inevitable losses when dealing with traditional computer hardware architecture.

