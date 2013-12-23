---
layout: page
title: NSTableViewBug
---

Create a NSTableView with some columns in InterfaceBuilder.  Now enclose the NSTableView in a NSTabView and you get these wierd little blocks where the columns don't draw!  This screenshot is actually from interface builder, but the same glitch shows up in my program.  How do I fix this?  Is there a good work-around?

http://www.timestretch.com/ib-bug.gif


----
File this with Apple's Bug Reporter (bugreport.apple.com).

----

I filed a bug with Apple's bug reporter, and it's been over a month, and no word.  Does anyone know what I can do to work around this problem?


----

Did you submit your nib? This will help them. At any rate, I solved this exact problem by deleting the table and adding a new one. The table itself becomes corrupt in the NIB somehow and replacing it with a fresh one seems to make it happy.

----

I can reproduce this problem from a brand new nib, every time.  Recreating the table does help, but it tends to get corrupted again.

----

Maybe you could save the working nib in text format, put it aside, and when it becomes corrupted diff 'em to figure out what went wrong.

Then, as a nasty workaround, you could add a build script that repairs the nib.

----

It looks like your column title is just too large. Does the problem recur if you use nice short titles?

