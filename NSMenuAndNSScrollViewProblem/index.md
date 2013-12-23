---
layout: page
title: NSMenuAndNSScrollViewProblem
---

I have a custom NSPopUpButton which works perfectly, but it needs to be in an NSScrollView. When I put it in an NSScrollView however, the NSMenu doesn't show up when I click the NSPopUpButton, even though it does if it's not in the NSScrollView. I know it's calling the method to show the menu, because I tested around with the NSPopUpButtonWillPopUpNotification. Can anyone help me?

----

Sounds like a bug.  Please file a bug report in addition to looking for workarounds.


----

A quick test using Interface Builder's Test Interface feature shows no problems. I took a popup button, put it inside a CustomView, embedded that in a scroll view, and everything works fine. This is on 10.4.

----

Yeah, I forgot to add, it works fine if I test the interface. But when I build and run the program, it has the problem. 
It's using a custom NSPopUpButtonCell. Would that have something to do with the problem?

----

*I don't know, does it work if you use a regular one?*

----

Weird, now everything works fine, and I didn't even change anything. Could this be a bug in IB?

----

Now the popup button's NSMenu is randomly working and not working. Sometimes it's working, then I do something completely irrelevant, such as changing the background color of an NSTextView which isn't even connected to the popup button by an outlet, and then the menu doesn't work. Does anyone else have this problem?

