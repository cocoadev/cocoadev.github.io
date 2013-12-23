---
layout: page
title: MouseEnteredSlowToUpdate
---

I have created an NSView  and created multiple tracking rects for mouseovers. The mouseovers work. I recieve the message on everyone. The problem is that they seem a little lagged. Right now I have about 300 items that have a tracking rect associated with them, and they are rather close (only 4 pixels apart). It seems that when I move the mouse quickly it sometimes does not have time to send the event and it gets lagged up. For example, I'm hovering over one item and a string is drawn at the top of the view represents that I'm hovering over this item. I then move my mouse quickly to an item that is in the top left. When I do this, I go over many other tracking rects. When i finally stop at my destination rect, i often don't see it's name up in the string at the top. Usually it's one of the other tracking rect's names that was along the way. What I would really like is an effect like in the Dock, where rollover instantly puts the name above.

I hope that that expains it well. Thanks in advance for your help.

----
Without going into technical details, I'd say your problem is that you've got 300 tracking rects inside a single window. That's bound to slow things up. 
Perhaps you could try a different way of tracking where the mouse is... are the tracking rects all the same size, for example? In that case, you could calculate which mouseover-area the mouse is in by just dividing its (x,y) coordinates by the width and height of your grid units...

----
I was thinking about doing that, but then how would I tell when it moved from one of the 300 items to another one. The code would only be called the first time it entered the rect. I could move the mouse over all the 300 items and it would just be called the first time. Would it be good to do that and then the mouseEntered: method tells the window to start accepting mousemoved events. That way I can tell when it moved and to where, and only listen for those events when I care (when the mouse is inside the large rect). Would that make faster execution?

----
In my experience, accepting mouseMoved: events is nice and fast. I've used it to implement live selection of objects inside complex OpenGL views, and it works just fine even on an old 300MHz G3 iBook.

