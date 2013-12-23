---
layout: page
title: UnderstandingQuartzTwoD
---

 

Hi,

I'm just getting into drawing into my own subviews using General/NSBezierPath and General/NSView's drawRect: method, and was looking through some documentation to see if I could do some more complex stuff like gradients etc.

This is when I hit upon this Quartz2D stuff, which I am slowly reading through trying to understand how all the pieces fit together.

Perhaps someone can take a look at the way I understand it at the moment and point out if I am going wrong...

General/NSBezierPath and General/NSView (and General/NSString when I do drawAtPoint: etc.) kind of abstract me from messing with Quartz2D directly for simple drawing. when I call specific methods of these objects (I'm thinking of General/NSBezierPath now) the object acts as a bit of a middleman, talking directly to Quartz2D on my behalf, to allow me to get simple drawing donw just a bit quicker and a bit simpler.

Quartz2D can be...er... set in a 'mode' which dictates the type of drawing output it gives from a specific set of drawing instructions, this 'mode' is called a context, and when drawing on-screen in my application I am using a 'window graphics context'.

All the drawing I can do with General/NSBezierPath I could do with manual instructions to my Quartz2D context if I so desired.

General/NSView knows about its Quartz2D graphics context, and uses this to draw itself.

***

All the above is 50% guesswork, 50% what I have managed to take away after reading the Apple docs. I find this method of learning, where I explain as best I can what I think I know, and let others pick holes in it to be extremely helpful, so if you'd like to expand upon any errors I have made I would be so grateful to hear where I have gone wrong in my conceptions. Equally, it's just as useful to hear which bits I have understood correctly.

No doubt there may be more concepts that I may have to check back with to make sure I am on the right path... but thanks for any help in affirming these ones...

- Peter

----

Hiya Peter,

Cocoa provides an excellent abstraction layer for the Quartz2D API's.

General/NSView is responsible for maintaining a graphics context, configuring its transformation matrix before drawing, providing subclassers with a hook into the drawing pipeline (after the context has been configured), and cleaning up all of the above. The General/NSApplication runloop takes care of tracking dirty rects and refreshing General/NSView instances that are in the view hierarchy.

General/NSGraphicsContext is a hook into the Cocoa graphics subsystem providing access to the low-level context of an General/NSView. This will allow you to use Quartz2D calls in cooperation with Cocoa.

Hope this helps.

-- General/EliotSimcoe
