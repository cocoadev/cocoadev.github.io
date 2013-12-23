---
layout: page
title: WindowFollowingWindow
---



In an app I m playing around with, I am trying to make a window follow another window (without requiring this target window to know about the follower). So my only chance to follow this target-window's movements would be to observe its notifications. However, General/NSWindowWillMoveNotification only informs me of the beginning of this movement, General/NSWindowDidMoveNotification only of the end- worse even, not even the end of the movement as defined by mouseUp, but also "premature" after the dragging pauses a bit. 

Wouldn't it be nice to see Apple implement this in a fashion like it's done for live resizing, where General/NSWindowDidResize is posted throughout the resizing, so you can track the process live?!

What I have done so far is to catch the General/NSWindowWillMoveNotification, run a while loop in which I update the follower window's position to match the one of the target until I catch an General/NSEvent matching the General/NSLeftMouseUpMask and then break out of the loop. This works fine for ->ONE<- window following and also only in case it is acceptable to block the mainthread while dragging.

I could think of some nasty and questionable tricks (putting the tracking code into a separate thread- although I failed with my initial attempt to do so) or implementing a controller class that keeps track of all my follower windows and handles the tracking of the target movement in its single while loop (again blocking the main thread)...

But can anyone think of something more straightforward?? Anyone maybe even has done something alike before?

-- marcocoa

----

Can the "follower" set the "leader" as its parent? Then dragging should work automatically. Look at     - (void)addChildWindow:(General/NSWindow *)childWin ordered:(General/NSWindowOrderingMode)place in General/NSWindow. --General/GrahamCox

----

This is one of the cases, where I would have told other people to RTFM...I dont know how often I ve looked over the General/NSWindow API reference but this one repeatedly escaped my attention! Maybe its just too easy- in German, there is the saying "Not seeing the forest because of all those trees"...

Sorry for the unnecessary question and THANK YOU! This saved me from investing another night into implementing a controller object in a new thread!

Just if anyone is interested in this for some other problem, that might not be solved by using addChildWindow:, I have made the solution work that I mentioned above, namely intercepting the mouseDragged and mouseUp events and doing the position tracking in a seperate thread for each "follower" window. This is cool for one follower, but nasty for more than two. Still, in principle it works and allows for continuous, position dependent action while dragging a window around!

--marcocoa

----

Glad to help. Actually I sometimes find people a bit too quick to yell "RTFM", because this sort of oversight is all too easy - I do it all the time. I think the difference is detecting laziness versus just not seeing the answer - obviously in this case your solution was far from lazy! --GC

----

I have added a post to my blog ( http://web.mac.com/mabi99/iWeb/marcocoa/ ) with some sample code to illustrate how to track changes of a window's frame using a second thread. Useful whenever you dont simply want to have one window follow the movement of another window. Might be be worth looking at for one or the other, it definitly was worth writing it for me (now I know how things work)! :-)

--General/MarCocoa
