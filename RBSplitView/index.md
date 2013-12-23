---
layout: page
title: RBSplitView
---



RBSplitView => [http://www.brockerhoff.net/src/rbs.html] A NSSplitView replacement that, hopefully, fixes all problems people have been complaining about in NSSplitView ;-). Creative Commons Attribution license, full source code, IBPalette, documentation. Send feedback to Rainer Brockerhoff => [mailto:rainer@brockerhoff.net].

Here's the description from the docs:
RBSplitView is an alternative to Cocoa's NSSplitView. NSSplitView is getting long in the tooth, being essentially unchanged from its NeXTstep incarnation, and has serious limitations, due (IMHO) to its basic design and overreliance on delegates. RBSplitView aims to solve most of the problems I (and others) have been battling with in NSSplitView, however it's not a drop-in replacement. It offers repeatable proportional resizing, animated collapse/expand, individual constraints on each split, collapsing by double-clicking or method calls, nesting split views with two-axis thumbs, saving and restoring state in the user defaults, and many other facilities.
Version 1.1.4 published September 1st, 2006.

----

Having played with RBSplitView 1.1 overnight, I have started integrating it in my app. Certainly works as advertised. In fact it is quite complete, as far as I can see. Works fine in Tiger, too. Bye bye NSSplitView!

One thing to be aware of is that it is not not quite as simple to use as a regular NSSplitView when it comes to pack and unpack. You need to drag your component views into or out of the subviews manually. This is not a major problem, but perhaps a future version could support unpacking?  In the meantime I'll still be using NSSplitViews for my prototyping, and when the design is locked in I'll replace with RBSplitViews where appropriate. It's a bit of extra work, but it pays off in code simplicity. 

One very nice thing about RBSplitView is the background colour transparency support - why does this work when Apple's equivalents don't?

Thanks Rainer!

- AlexClarke

-------------------------

Really great effort. Very good. 

Thanks Rainer

-Pintoo

----

Has anyone else experienced issues using RBSplitView with IB version 2.5.1 (439) from Xcode 2.1? I have run into issues with putting an NSTableView into a subview renders the NIB corrupt. 

Just for the record, same thing happens here with Interface Builder Version 2.5.4 (446) and RBSplitView 1.1.3 (May 21, 2006). However, I think RBSplitView is good enough that I am working around it, building my nib with NSView placeholders inside of the split view and swapping them with table views at runtime. I wouldn't be surprised if this issue is actually IB's fault somehow... IB certainly has many other crashing and nib-corrupting or otherwise nib-mangling bugs...

