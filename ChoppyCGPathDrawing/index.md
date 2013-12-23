---
layout: page
title: ChoppyCGPathDrawing
---

I have some drawing code that interacts with a library that returns paths created via Core Graphics; and I noticed that when I scroll around in my document I start getting artifacts like the following. It would seem that the anti-aliasing is fubar. Anyone out there know how to resolve this?

http://homepage.mac.com/vannote/images/ChoppySnap.gif

Thanks,

- NeilVanNote

----

I saw similar artifacts in a graphing program I was developing -- it only showed up when I had zoomed in and scrolled around. This happens when a segment of the line is drawn two or more times over the same spot -- the antialiasing overlaps additively and the pixel goes to full saturation.

Your best way around this is to either manage your clipping rects better, or to make certain your whole bounds ( not the ClipView bounds ) are fully cleared. There will be a performance hit since you can't rely on pixel copying for scrolling. In fact, you can control that via NSClipView's setCopyOnScroll: (BOOL) method.

In my case, I manually constrained which paths were drawn by intersecting them with a manually generated clipping rect, and for every call to drawRect I cleared the *whole* view and drew only which paths were visible. I turned off copy on scroll too. When I did this, the artifacts went away. YMMV.

--ShamylZakariya

----
Thanks Shamyl, Setting - setCopiesOnScroll:NO in my NSScrollView subclass - initWithFrame: did the trick.

Unfortunately, this has had the side effect (as you mentioned) of extending my -drawRect: clipping rect to the entire visible portion of the view even if I should only need to draw a small uncovered portion. :-( Too Bad, I guess... The path drawing is in a transparent overlay view that sits on top of a primary view (that is not so simple). I don't mind so much about the overlay's clipping rect being extended, but the view under it is taking a performance hit I really didn't want.

Cheers

- NeilVanNote

----
New related problem...

Now, It would seem the path is drawn correctly without the overdraw artifacts, I have a weird problem that again is best explained visually.

Here is a sample path shown correctly (IMHO).

http://homepage.mac.com/vannote/images/PathResizeSnap1.jpg

If I scroll to hide the top/left corner, the drawn path widens.

http://homepage.mac.com/vannote/images/PathResizeSnap2.jpg

Ok, call me a perfectionist, but it is quite noticeable (to a user), and undesirable as far as I am concerned. And guess what happens if I scroll back to reveal the corner? Yes, It is drawn correctly again.

Any Joy?

- NeilVanNote

----

Well, without knowing how you handle scrolling it's hard to make a guess. That said, looking closely at the image in photoshop what it looks like is that the line's width is growing by as much as a half pixel total. Which makes no sense at all.

So, I guess there's a couple things that could be happening here, dependent on how you're drawing. First, when you drag the scrollbar, are you using standard NSClipView transformations? E.g., are you just letting the scrollbars automatically manage a view that's larger than the container scrollview? Or are you using manually inserted NSScrollbars to change the origin of a coordinate system you maintain? If it's the latter, progressive floating-point error *could* explain what you're seeing...

That's off the top of my head. Perhaps you could show some code?

--ShamylZakariya

----

Actually, this is pretty straight forward to reproduce. A simple NSView subclass, placed into a subclassed NSScrollView (subclassed for unrelated reasons in my project). I am not doing anything special with the coordinate system nor transformations (yet). The NSScrollView handles all of the scrolling. I will throw together a simple test project that reproduces the problem and post a link to it.

- NeilVanNote

----

Ok, So it wasn't so easy to reproduce. ;-)

Fortunately, trying to duplicate the problem has led me to the cause. The path points are coming from an external device and run through some pretty heavy computation before being displayed. In the end, none of the points were integral. Running the point coordinates through floorf while building the NSBezierPath has fixed that and the problem.

Interesting how it manifests itself, but I guess it makes sense since only a partial path (I suspect) is being drawn internally by NSBezierPath or Quartz.

In fact, this may have been the root cause of my other problems outlined previously. Now to backtrack and start undoing some of the other changes. I would love to get my partial clipping rects back. ;-)

Thanks

- NeilVanNote

----

To follow up on my own reply;

The path widening still occurs in certain circumstances. The more I think about it, I don't believe I can get around it. All of the lines are slightly angular (over a very large view) and with Cocoa optimizing the path drawing, there will always be non-integral points being calculated, if not by me, by the drawing system itself.

- NeilVanNote

