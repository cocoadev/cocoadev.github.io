---
layout: page
title: NSSplitViewDrag
---


I want a object to follow the edge of a splitView when the splitView is resized,

but when i subclass and override mouseDragged or mouseMoved nothing happends,

what do i have to do?
----
It sounds like you just need to set autosizing in Interface Builder. Or am I misunderstanding you?

----

Try RBSplitView if you haven't already.

----

*Or am I misunderstanding you?*

Autosizing didnt work out the way i wanted.

*Try RBSplitView* - I will try it:)


----

splitview has a delegate method 

splitView:resizeSubviewsWithOldSize:

it gets called after the splitview is resized, and it allows you to manually resize your subVeiws at the appropriate time.

this is all you should need to manually setup a subView, but splitview Is also very flexible through the delegate methods, I suggest looking up NSSplitView in the documentation, where all of the delegate methods and Notifications are described in detail.

