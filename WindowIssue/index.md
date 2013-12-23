---
layout: page
title: WindowIssue
---

I have been coding Cocoa for many a year now and was just writing an app for someone else...but I highly embarrassed myself.

Here is the dilemma. I have three windows that I need to display at varying times based on load conditions. None are set for 'Visible at Launch Time' and therefore I am meant to control them via code.

So I have my class setup and instantiated with outlets to the three windows. I call [windowName makeKeyAndOrderFront:self] on them and of course nothing happens.

I have retained them but don't really feel like chopping them down to their contentView's and displaying as newly created windows (this is my normal method).

How do I go about displaying these windows? I am demonstrating this to a new programmer and its quite embarrassing that I don't know how to do it.

Thanks in advance. I owe you all one.

----

ehm... why don't they show up when you send them a makeKeyAndOrderFront? this is how I go about windows that are set not to be visible at launch time (with the exception of dynamically loaded windows, which I keep in their own nib and load with an General/NSWindowController).

----

Are you sure your outlets are connected to actual windows? Have you logged window descriptions of all three windows?

General/NSLog(@"window1: %@ window2: %@ window3: %@", [window1 description], [window2 description], [window3 description]); 

If your windows are talking back to you, then I have to agree with the comment above.... ehm... why don't they show up?? The only other thing I can think of is maybe the frame origins are getting messed up somewhere and the windows are getting positioned by the table lamp next to your monitor :-)

----

Yeah, for some reason my frames were being placed on a 'second monitor' on this system even though there isn't one. A quick alignment to 50,100 brought my windows back in view. Thanks everyone.
