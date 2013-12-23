---
layout: page
title: AutoResizeViewInCodeProblem
---



having problems with autoresize.

setup:
I have a view with two subViews.
the parent view is set to auto resize in IB, so that it maintains the same distance from each edge of the window
the children are the problem.

if I set them in IB to auto resize, everything is dandy.
when the window resizes, so does my view and its subViews.

but I NEED to set this in code.

so 
http://developer.apple.com/documentation/Cocoa/Conceptual/DrawViews/Tasks/MovingResizingViews.html#//apple_ref/doc/uid/20000910/BBCFFIJE

says to use 
setAutoresizingMask:

I built my code thusly,
        when i add a new subview to my view, the view sends it this message:
	[newChild setAutoresizingMask:NSViewMinYMargin];

it has literally NO effect.  
when I compile and run, there are no Auto resizing in my subviews.  

am I doing this wrong? or does anybody have any suggestions?
----
Try calling     resizeSubviewsWithOldSize: on your (top-level) view. Also, you really shouldn't create two pages for the same topic.

----
  
this is already supposed to be called by the top level view, during resizing... according to the docs.  as far as the reference is concerned, all you "should" have to do is tell the subviews how they auto resize, and the default behavior is that they then Just do it.

here right from the docs:
"Autoresizing is accomplished using two methods. resizeSubviewsWithOldSize: is invoked automatically by an NSView whenever its frame size changes."

as of right this minute, that is not happening, so either I hit a bug, I have my code snippet wrong, or the docs are not correct. I've already put in a bug report, but as my first bug report is about 4 months old and nobody has acknowledged it yet, I have low faith in the process at this moment. I think apple thinks it can outrun some of these bugs.

my bad about the other page... but in safari on two computers that page just isn't there. It never showed up in the discussions after I posted it.

----
solution:

I made a test app.
I did some stuff that should work, then tried stuff that shouldn't work.

in the end, as wild as it seems... the answer is:
[myView setAutoresizesSubviews:NO];
put that near where you set the autoresize mask.

this Turns Autoresize ON, and then allows you to Programatically alter the "sproings"

I think maybe that may be too simplistic, because when i did this:
[myImageView setAutoresizesSubviews:YES]; 

without ever first setting it to NO, 
my IB settings take effect.

at any point if I set it to NO, my programatic settings take effect.

its is seriously ODD behavior.

