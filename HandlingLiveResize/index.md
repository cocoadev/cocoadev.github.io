---
layout: page
title: HandlingLiveResize
---

 

I need a view to be notified when the user clicks the mouse button in its window but outside the view.

Thanks in advance

Set up another view to catch the clicks.  If you're wanting clicks on the whole window, you can use your own view for the window's content view.  This other view can then either message your view directly (say by calling heyAMouseGotClickedOutSideOfYouFiguredYouWouldLikeToKnow:, or by posting a notification)

----

The above won't work if the user clicks in a view other than the content view in the same window (not that it's entirely clear what behavior you desire; maybe a clarification is in order?) ; To get all mouse clicks in a window, subclass NSWindow, override     -sendEvent: and then filter out the ones you're interested in.  -- Bo

----

I have to re-calculate the view's drawing coordinates when the user resizes the window. The problem is that NSWindowDidResizeNotifications are sent during the entire resize time, and everything goes too slow. So i think is better to re-calculate only when the user has ended the resize, and thought i could know when it happens detecting if the mouse has been released in the window after having received some NSWindowDidResizeNotifications. I've try too with NSViewFrameDidChangeNotification and NSViewBoundsDidChangeNotification but i had the same problem.

----

Oohhhhhhh.  Well forget all that preceding nonsense.  Just override your view's     -viewDidEndLiveResize method and recalculate in that.  Also check out the     -viewWillStartLiveResize and     -inLiveResize methods.  -- Bo

----

Ok. Now it works. The answer is always simpler than what my silly head gets. Thank you very much Bo.

----

This discussion was initially entitled G**'etMouseNotificationsFromOutsideTheView
and is an instructive conversation for folk posting questions.  If you post what you want to accomplish (knowing when a window resize ends) rather than a bit of detail on the path you think is the right one, but isn't (how do I detect a mouse up outside of my view), we can get to your answer a lot faster.

