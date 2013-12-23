---
layout: page
title: HidingOnlyPartOfAWindow
---




hi...


i want to move a window with nsanimation off the main screen. it is supposed to simply slide horizontaly out of the main screen (the one with the menu bar, ehhh).
all works fine, except that if the user has a second screen my window naturally shows up on this second screen. this kind of misses the point, i want it hidden. 
so i am looking for a way to tell my window to be only drawn on the main screen and never show up on the second on.


so my questions:

    -how to tell the window to draw only a part of itself(sort of setting a clipping region), while it is animating its way out of sight, leaving out everything that already moved to the second screen

    -is my design fundamentally wrong, because it feels awfully dodgy. if so what to do to get the impression a window moves out of the screen without showing up on any other than the main one.




many thanks in advance

Kolja

----

I don't believe this is really possible in a way that would feel right to the user. Macs treat all screens as one connected space, so the user expects a window to move onto another screen when moved that way - this is what will happen in all applications except yours (assuming you could get it to work). In my experience applications that fundamentally don't behave according to expectations get trashed pretty quickly. What might work a little better would be to find the overall bounds of all of the screens, and move your window off the edge of that. On a single screen system, it would behave the way you describe. On a multiscreen system it would cross the second screen but at least that would be the expected behaviour even if it looked a little odd. I can't think of a way to implement what you have described even if you chose to ignore this advice. --GrahamCox

----

I had come to the same opinion as you originally. The concept is to create a "Hot Area" along the border of the main screen similar to the build-in Active Screen Corners. You move the mouse into a special area and a window slides out. You can than perform a quick task in the window and if you move the mouse away, the window slides back in and is not cluttering your screen. I want it to be a quick action, so the ways have to be kind of short. I comforted myself with building in the possibility to let the user decide where he actually wants the Hot Area to be. You can move it on any screenborder on any display. This kind of brought me at ease.

Kolja 

----
If your window doesn't have a title bar or shadow, you have a few options:

* Swap the window's content view for an image of the view hierarchy that you then clip as necessary.
* Turn off autoresizing for all of the content view's subviews for the duration of the animation, and resize the window to clip them.
* Keep the content view itself at the same size while you resize the window. Although I'm not sure if NSView will let you do this.

If your window does have a title bar, you'd swap the window for one that's borderless, and draw a title bar in addition to the content view.

It's a bigger problem if you window has a shadow. In that case you'd have to draw the shadow yourself too, in order to clip it.

That said, if your hot corner/edge is on the boundary between two screens, it's not actually a corner or edge, is it? Any mouse trying to enter it would overshoot onto the next screen. From what I understand, you should require the user to pick a real corner/edge.

----


Yip, I am convinced. I go for real edges. Thanks for your time.

Kolja

----
How would one implement "hot corners / edges"?


----
I have a demon app with an seethru window. In there I put a custom view with an trackingRect. Just put your window where you want the hot corner and you are done.

Kolja
----
Yes, seems reasonable, thx. How do you think Apple does it? As far as I can tell, they don't use a translucent window (tested it by trying to make a screenshot of it with command-shift-4 and then hit space, you then can take screenshots of whole windows...)

