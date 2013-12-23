---
layout: page
title: NSWindowButtonMessageInterception
---

Hi all,


A quick post to see if anyone can help me out. 
Is it possible to do in cocoa when findWindow() and FindWindowEx() do in MFC, which in this case would be to find the subwindow of a separate application and at the very least find it's rect, and at the very  most disable the window from accepting commands.

Any help on this would be greatly appreciated.



Thanks 
-W

----

This does not make sense under Cocoa, or on the Mac in general.  You're trying to shoehorn your Windows thought process into a completely different operating system.  Also, see MailingListMode.

----

I really have no idea what you're asking, since I don't know anything about Windows. It would be good to actually learn a little bit about the platform you're targeting before you start to seek help. For example, Macs do not have subwindows; the Windows concept of every control being a window simply doesn't apply.

----

With some limited exceptions, applications on Mac OS X do not have the right (or the ability) to disable components of other applications. Perhaps you should focus on what you want to accomplish (the big picture) and figure out a platform-appropriate way to get there.

----

It always struck me as a huge shortcoming of windows (from a security and abstraction standpoint) to expose other apps' interfaces to everyone.

----

I think that it's a holdover from when Windows did not have protected memory. Applications could bug each other directly, so Microsoft probably figured it was best to give them tools to do it safely (so that developers would not be encouraged to dig into undocumented memory guts.)

----

I agree with the above assessment, **however:** Check out Application Enhancer and consider that as a means to intercept events as they enter the application, then decide whether to allow them through. Since all mouse and keyboard events are passed from the window server to the application instance, that is an excellent place to insert a filter "haxie". I will say personally that I do not like Application Enhancer and steer clear of APE modules on my own system (not because Application Enhancer is itself evil, but APE modules, unless written with great care, can cause no end of confusing problems with your applications / system). Nevertheless, that is most definitely a way of accomplishing your goal.

Now if you do this, be prepared for most users to avoid your product. This is *VERY* un-Mac-like behavior. Application "A" is expected to keep its damned hands off of application "B". If A wants something of B, it damned well better ask nicely (via AppleScript) and handle any rejection gracefully. Anything less is uncivilized behavior in OS X.

