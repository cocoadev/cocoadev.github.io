---
layout: page
title: UpdatingNSTextViewWhileLoopIsActive
---

I have a project that runs a rather processor-intensive loop (it reads and archives IPTC data on photos while archiving them both to an archive server and a CD). During the loop, I want my project to update an General/NSTextView with a single line that says "archiving (photo filename)..." so the user knows something is actually going on.
I can get the General/NSTextView to display text in other parts of the program (for instance, if the user hasn't inserted a blank CD, the textview displays text instructing the user to insert a CD before he/she can continue). The method I use to display that text is the SAME method I call at the end of each iteration of the archiving loop, so I know the method itself works, but it doesn't work while the loop is active.
Is there something I'm missing that would tell the General/NSTextView to display its text each iteration of the loop?
If I haven't explained this issue well, please let me know.

----
By default, views are updated at the end of each iteration of the run loop. But it seems your task does not let the run loop do its stuff because you stay inside your method the whole time during the archiving. You probably also get the spinning beach ball after 5 secs of that.

This is a classical problem. You need to either run your archiving in a separate thread, or cut the processor-intensive task in small chunks (and run each chunk separated by a General/NSTimer with a time interval of 0). As an alternative, if you don't mind letting the UI unresponsive, be stuck in the loop and still have the text field updated, you can call the     display method which should trigger immediate update of the text (when you use     setString:, a call to     setNeedsDisplay: is also made, but the actual display is actually deferred until the General/NSRunLoop iterates). Also, you probably should use an     General/NSTextField instead of     General/NSTextView, for just an info message like that.

----

Thanks for the response. Can't use an General/NSTextField because it's more of a running log file, not a single-line status display. 
The actual method itself is small, calling multiple other methods to do the actual archiving, reading, writing and logging. 
The General/NSTimer idea looks promising. I've never used one. Can it be created in code? (I'll read the documentation while awaiting an answer).
The archiving, of course, does actually work while everything is unresponsive (and you're right about the beachball). This is not a consumer-designed project, so I may just let the thing stay unresponsive if I can't solve this after a while.
The loop actually seems to take precedence over other running processess so that they're unresponsive, too.
The loop automatically exits when the CD is full, so 700 MB, give or take a few, is the limit and the loop will exit on its own.
Thank you so much for your response.

----

Read General/BookBuildingCocoaApplications, especially the chapters about running a task in the background and a third thread to prevent deadlock.
It is a classical problem, like the previous poster asserted, and not that difficult. I don't think I would try to combine this with General/NSTimer.
