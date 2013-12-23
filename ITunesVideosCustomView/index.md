---
layout: page
title: ITunesVideosCustomView
---

This may be an easy question and I'm just missing the concept. What would be the best way to create a custom view ala iTunes Videos where you have your objects grouped in a matrix-esque style, almost like pagination, where they fill according to width of the pane? Delicious Library is famous for this with its cover view.

I can't seem to find a good way to implement it. I've looked into subclassing NSView and drawing everything myself. If I do that, I'd have to set up selection, dragging, and everything to fit the paradigm, almost like re-implementing NSTableView completely, but for a different design.

Is there a better way anyone else has worked out to do this? Thanks, --LoganCollins

----

Just a hunch, maybe a bad one, but does FlowLayoutView do anything like what you're interested in?

----

You want to use NSMatrix with your own custom NSImageCell that probably draws its own selection border and stuff. NSMatrix will handle all the dragging, selection, etc. associated with the view. For example, the thumbnail view in the media browser for iLife/iWork is an NSMatrix with a custom NSImageCell that displays an image and below it a filename.

--ZacWhite

----

Thanks! I'll get to looking through NSMatrix right away. I guess when I said a "matrix-esque style", I should have looked into NSMatrix a little more. --LoganCollins

----

Okay, I've been playing around with this all day, and I have hit a snag. I am trying to bind the NSMatrix to an NSArrayController to give it content. I have the matrix bound to the controller's content fine, but I can't seem to get selection to work right. I can't bind any of the selection bindings found in IB programmatically without the debugger throwing an exception. It almost seems like none of the selection bindings actually exist, because that's what the run log is saying. Anybody know how to bind an NSMatrix to an NSArrayController correctly? --LoganCollins

----

I think you can find a part of the answer here BindNSMatrixWithNSArrayController but I have difficulties to bind pictures.

