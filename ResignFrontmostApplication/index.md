---
layout: page
title: ResignFrontmostApplication
---

After ForceFrontmostApplication, is it possible to resign from the position of frontmost application, and return focus to the previous application and object? I suppose before forcing anything I could see who is frontmost, then return to it, but that might be a messy way to accomplish this.

-- RyanGovostes

----

Your approach is correct; you need to save the frontmost app before making yourself frontmost, then reactivate it when you're done. There is no public API for finding out who is second-frontmost or anything like that, so it's the only way to do it.

Wrong. The process manager keep processes ordered. So if you make your process the front process, the old front process become the second process, and you can get it easily with GetNextProcess() function.

----

Alright, here's what I came up with:

    #include <Carbon/Carbon.h>
ProcessSerialNumber lastFrontProcess;
GetFrontProcess(&lastFrontProcess); // stores the last process
SetFrontProcess(&lastFrontProcess); // restores focus to it


Now, I call     GetFrontProcess() in the NSApp's delegate's     - (void)applicationWillBecomeActive:(NSNotification *)aNotification; function. It appears that this function is called after the process is already frontmost, since getting the process name of     lastFrontProcess returns the name of my application. Looking at the NSApplication docs, there doesn't seem to be a way to be notified before the program becomes frontmost, or is there? -- RyanGovostes

----

Since switching the frontmost app happens asynchronous to your application, I guess it makes sense that you couldn't catch the event before the switch happens. I didn't realize you were just waiting for your app to be activated by the user; this is a trickier problem.

My advice would be to sign up for notifications that tell you when the front app changed, and store that value somewhere. You'll get a lot more notices than you really need, but that's not a serious problem. Then when your app activates, you can look at the last app that was frontmost before you, and activate it.

You can find out how to get notifications when the front app changes at http://www.unsanity.org/archives/000045.php

----

Calling     GetFrontProcess() in     applicationDidResignActive: seems to work correctly.  Any one have an idea why it doesn't work in     applicationWillBecomeActive:, is it a bug?

*What is "correct" in this case? I would expect that it would return the other application in both calls.*

Yeah, I forgot to mention what I thought was correct. Calling     GetFrontProcess() in     applicationDidResignActive: returns the other application (that is the application being switch to) and calling     GetFrontProcess() in     applicationWillBecomeActive: does what was described above, it returns my application and not the other application.  I was expecting to get the other application in both cases.

----

Has anyone succeeded in doing this? I haven't been able to do the stunt. -- JesperNoehr

FYI I never got this to work, so I tried with     [NSApp hide:self];, and that did the trick. In my faceless app fading out, at least.

