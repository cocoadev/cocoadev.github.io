---
layout: page
title: DrawingAnimation
---


Hi,
I've just started using Cocoa, and have probably simple question (however I didn't find an answer yet).

How can I create simple program that draws rectangle in every x second.
My actual app redraws NSView every x second. How can I just add new points instead of redrawing all of them with for statement? http://goo.gl/OeSCu

    
#import "AnimationView.h"

@implementation AnimationView

- (id)initWithFrame:(NSRect)frame {
 
    self = [super initWithFrame:frame];
    if (self) {
        iterator = 1;

	[NSTimer scheduledTimerWithTimeInterval:0.1
		 target:self 
		 selector:@selector(stepAnimation:) 
		 userInfo:nil 
		 repeats:YES];
    }
    
    return self;
}

- (void)drawRect:(NSRect)rect {
	iterator++;
	NSRect shape;
    NSBezierPath *bp;
	
    [[NSColor redColor] set];

	for(i=0; i < iterator; i++) {
		shape = NSMakeRect(5, 5, 1, 1+i);
		
		bp = [[NSBezierPath bezierPathWithRect:shape] retain];
		[bp fill];
	}
}

- (void)stepAnimation:(NSTimer *)timer
{
    [self setNeedsDisplay:YES];
}
@end


Thanks.

----

It's not very clear what you want. From your code it seems as if you want a rect to appear to grow larger in the downward direction as time progresses. Assuming that's the case, instead of keeping the 'iterator' ivar (I assume it's an ivar), keep an NSRect ivar instead and change its height in the timer callback before you call setNeedsDisplay. Then the drawRect method just fills the rect whatever it is and doesn't require a loop. This isn't really a cocoa question, it's one about how to sensibly structure an object-oriented design. The answer should be obvious with a few moments thought. --GrahamCox

----

Sorry. It wasn't good example. I have to duplicate those rectangles not in straight line, but let said randomly. I want to create Langton Ant app. http://en.wikipedia.org/wiki/Langton_ant

Thanks.

----

Hmmm, rather different. There are a number of ways to handle this, but I suggest it can be solved in the same way. In this case you probably don't want to keep a list of where the "ant" has been at every time point in the past - eventually you'll find iterating over all those rects will be like wading through molasses. Effectively you'll be replaying the entire history of the animation every frame. Remember that windows are buffered. Provided you never deliberately erase them, whatever you drew there before remains there - a new call to drawRect will draw over whatever is already there. So at each turn, simply update the rect of the "ant" according to the rules, then call for a repaint. In the drawRect method, make sure you don't erase the background, and just draw the new rect. As it moves, it will leave a trail behind. You will need to arrange a flag that will erase on the very first frame, or when "resetting" the animation, so you can clear out the clutter. For Langton's Ant, since you also need to look at what is already drawn at a given pixel or square, you might also either want to keep track of the view area in a grid structure of some sort or perhaps use an image and look directly at pixel values. Hope this helps. --GC

----

Draw your changes where you need to make them, and then use     -[NSView setNeedsDisplayInRect:].  Since you're drawing rectangles anyway, this is a piece of cake.

----
I believe you have the actions backwards. You need to mark a rectangle for redisplay first, then draw that region in     drawRect: when the redisplay actually happens.

----
Also, beware of PrematureOptimization.      - setNeedsDisplayInRect: is an optimisation compared with just flagging the whole view for redisplay, though it's likely in this case that it's as easy either way. Get it working, then get it working fast. --GC

----
Yeah, it might be premature optimization, but at the same time it's trivial and fundamental to understanding how to avoid flushing the entire window buffer.  I think it's worth pointing out anyway.  And yeah, I got them backwards.  :P

----
May I ask for some snippets using setNeedsDisplayInRect ? - Thanks

----
Don't rely on your window's BackingStore, draw into an image and then draw that to the screen.
    
- (void)drawRect:(NSRect)aRect
{
	[image drawInRect:[self bounds] fromRect:NSZeroRect operation:NSCompositeCopy fraction:1]; // This will stretch the image to your view's current bounds.
}
- (void)animateOneFrame:(NSTimer *)timer
{
	[image lockFocus];
	// Draw the latest frame. It will apear on top of whatever was last drawn.
	[image unlockFocus];
	[self setNeedsDisplay:YES];
}

As for generating future frames, I would either use a 2D C array, or, the raw bitmap data, if possible (depends on the algorithm, haven't looked at it).

----

"Don't rely on your window's BackingStore". I disagree with that as a general rule, there are plenty of times when it will save you a lot of unnecessary heavy lifting when it comes to pixel pushing. The backing store is under your control, it only gets changed when you change it. Yes, this requires more understanding of what writes to it when but more knowledge is a good thing, right? If your advice to use an image is based on the feeling that you don't "own" the backing store, then you haven't fully understood the graphics system. Yes there are times when using an image is advisable (this might be one of those times), but *as a general rule*, no - add it to the "voodoo" techniques pile - that is, those techniques that you use for no good reason other than it seems to ward off evil spirits (or in this case, behaviour you can't explain because you are not understanding what's really going on). --GC

----
This is generally wrong, as far as I know. Something like minimizing and maximizing a window will often destroy your backing store, and nothing in the API guarantees that it will remain intact for anything, so relying on it to do so would be bad idea.

----
Yup. Your view can get redrawn at any time, including:

* De-minimize.
* Superview redrawing.
* Subview redrawing (if its     -opaqueAncestor is your view or above).
* Window resize (unless you override     -preservesContentDuringLiveResize).
* Obviously, if you move the view to a different window.


----

Right, but these are all well-defined operations. Understand what I'm saying - not that you should never use an image or other cacheing technique, just that you should understand when and why to use one. Minimizing a window shouldn't destroy the backing store - after all it's the contents of it that the window server uses to create the dock image. If you have a view that owns part of a window - and I mean fully owns it, in that there are no subviews and no superviews that show through it, the frame area is all yours. I agree that there are times you might prefer not to rely on this, but for the original poster's needs, using the backing store to cache previously drawn "ants" is perfectly valid and will give better performance than caching using an additional image and drawing it every frame. My point was that using an image "just in case" is voodoo. Use an image by all means if you know why you're doing it, and accept the performance implications, but don't do it because you don't understand the backing store. BTW, using a CGLayer (if you have 10.4 or later) will give much better performance than caching using NSImage. --GC

----
You say that minimizing a window shouldn't destroy the backing store but it always does by default. New windows created in Interface Builder are created "one shot", which means that the actual object in the window server is destroyed any time the window is not on-screen, and minimization qualifies, so that the window itself is effectively recreated from scratch when deminimized. Obviously this causes the backing store to be destroyed as well.

You are correct that these are all well-defined operations but the important question here is whether those are *guaranteed* to be *all* of the operations which will *ever* destroy the backing store. In other words, is there anything in the API docs which state that this particular list of operations is the full, complete set which destroy the backing store and nothing else ever will? If so, where is this list located? If not, then you simply cannot rely on it, because the API contract means that the system can whack your backing store and ask you to regenerate it any time it feels like it.

On the subject of speed, if you just have a bunch of rectangles, the fastest way to draw it will be to draw the rectangles directly into a bitmap, then upload it as an OpenGL texture and do your drawing with OpenGL.

----
"�and no superviews that show through it, the frame area is all yours."

This is incorrect. The superview redrawing will cause all of its subviews to redraw regardless of their opacities. (This is necessary because, for example, its bounds might have changed.) And there is a whole private view hierarchy above the window's content view.

By the way, I didn't know about     CGLayer (I'm still stuck in 10.3.9-support-land), but I'll start recommending it�it sounds nice.

----
Thank you all. I've finally created that app : )

----

*The superview redrawing will cause all of its subviews to redraw regardless of their opacities* Correct. But if that view is yours (i.e. a custom view you wrote) you own the area defined by its frame. If your view doesn't erase but is nevertheless set opaque, that area isn't overwritten in the frame buffer - effectively your view is clipped out from the view hierarchy that is drawn above it.

----
I just tried it out, and yup, that seems to work. But as far as I know, its UndocumentedGoodness. If     -isOpaque returns     YES, you are bound by the API to fill all of the areas needing display. So it probably shouldn't be recommended as a general technique.

---- Well, logically the result has to be as described. You are declaring that your view is opaque - i.e. nothing shows through it, yet you don't draw anything. The only way to reconcile this is for the view system to clip out your view when drawing the superviews. By setting     - isOpaque you're telling the view system "I own these pixels, keep yer mitts off!". This clipping out action may not be explicitly documented (or it may, haven't checked) but it can't logically be any other way. Not everything has to be explicitly documented for you to be able to rely on it - if it were a case of "forbidden unless permitted" the docs would have to be ten times the size they are. Also, don't forget that Cocoa's views sit on top of Carbon's window buffering, which is implemented using old-skool GWorlds. To allow QuickDraw to work within a Cocoa context a scheme is needed that protects those pixels "owned" by QD not to be obliterated unexpectedly. Also, performance is enhanced by drawing as little as possible, so it's definitely worth NOT overwriting pixels in the back buffer whenever possible - an optimisation that the view drawing system takes seriously.

----
This is completely untrue. By overriding     -isOpaque to return     YES you are stating that you will cover the entire area of your bounds when you draw. The API is then free to do anything it wishes which is compatible with that, including drawing all over your view's area before asking your view to draw.

QuickDraw is not related to normal windows on 10.4 and possibly earlier. Using QuickDraw in a window makes that window exempt from any Quartz 2D Extreme acceleration. While Q2DE is not yet enabled by default, it illustrates how QD is far outside the normal graphics path.

And yes, it would generally be faster not to draw in this area, but relying on code you don't own not to mess you up because messing you up would be *slower* is about the worst reasoning you can have.

----

Seems to me it's a question of how you're looking at it. Nowhere in the documentation does it say explicitly one way or the other. Therefore I take      - isOpaque to mean precisely what it says, no more and no less - i.e. that the view is opaque - it doesn't show anything through it. That is the English meaning of "opaque" and the meaning it takes here. Therefore if you return YES it does NOT mean you promise to draw that area (nowhere in the docs does it say this as far as I can see), it means that everything ELSE promises NOT to draw there. Maybe QD is outside the graphics path when Q2DE is used but that's not relevant here - maybe I shouldn't have brought it up. I'm not suggesting it's necessarily something you can or should rely on - but you can at present and for certain quick and dirty types of animation - such as the one the original questioner brought up - it will work perfectly well.

----
How about the *very first sentence* of the discussion in the docs for this method?

    A view object is opaque if it completely covers its frame rectangle when drawing itself.

In other words, if you don't cover your entire frame when drawing, then you're not opaque. If you're not opaque but you say that you are, then you have a bug.

This nonsense about other views not drawing over you is fantasy. Nothing in the documentation supports this notion. Views are not windows and do not have strongly enforced clipping between them. The behavior you're seeing is an implementation detail and an optimization which could change at any moment. Your interpretation of the English meaning is correct, but the only reason these views don't show anything through them is because they are explicitly bound to rewrite the entire backing store in the area that they cover.

Update: an even better quote, found in View Programming Guide for Cocoa -> Optimizing View Drawing -> Specifying View Opacity:

*If you create an opaque view, remember that your view object is responsible for filling all the pixels within its bounding rectangle using opaque colors.*

That section also states the main purpose of this method: invalidating an opaque view or one of its subviews does not require redrawing any superviews. Nowhere is it stated that superview drawing, when it does happen, must be clipped so as not to disturb opaque subviews.

----

*your view object is responsible for filling all the pixels within its bounding rectangle using opaque colors*. OK, accepted, but does it also state explicitly that this must occur on every redraw event? Because if it does not, then it's still a matter of how you look at it. It could be perfectly valid to say " I drew there (opaquely) last time, and I can rely on nothing having changed that without my knowledge". For compatibility with QD (however this might change in future) this must also be the case.

----
Round and round we go....

The whole point of the     drawRect: method is that it's invoked when some portion of your view has been invalidated, which is to say that its backing store is believed to no longer be correct. There is nothing in the documentation which states, either explicitly or implicitly, that your backing store will be preserved under any circumstances. If you do not completely fill the list of rects given to you in that method, and you return YES from     isOpaque, then you have a bug, full stop.

Compatibility with QD is useless to discuss, because I've already established that the system's behavior can and does change when you start using QD.

I really don't understand why this is so difficult to grasp. This is all very standard NSView behavior and semantics. You seem to be observing some implementation-specific detail, then deciding that it *must* work that way in *every* case just because nothing in the documentation *specifically* forbids the *exact* thing that you're doing. This is backwards. If the API does not *support* the semantics that you rely on then you simply cannot rely on them.

----

It's not difficult to grasp at all. To some extent I'm playing devil's advocate - I wouldn't go out of my way to set up a system this way myself for a typical view because it's not the standard approach and that will often come back to bite you. What *you're* not understanding is this discussion is in the context of the original question, which is about implementing Langton's Ant. In that case the drawing "events" are being driven from a regular timer and that has nothing to do with whether the system has detected that part of the backing store is invalid - it's just a moronic request for an update when likely as not none is needed (except for drawing the new position of the "ant"). Thus if you want a reasonable (but to some extent quick and dirty) implementation of Langton's Ant that requires minimal set up and maximal performance, just use the backing store as a place to store already written pixels that you can subsequently forget about - i.e. you don't have to worry about storing them elsewhere as well, e.g. in an image. It works perfectly well. In a similar way, some apps can also rely on the backing store as a place to composite an image into without requiring an additional buffer then transferring that to the screen, incurring a wasteful and potentially expensive copy of a large number of pixels. If the view needs an update just recomposite the image from its components. I have seen a number of apps where the programmer carefully composites a series of components to some intermediate store, then copies that to the view. It's simply a waste of time. Usually getting rid of that destination buffer will improve performance substantially. It doesn't add up to breaking any of the documented semantics in any way, it's simply a more efficient pathway. But coming back to your last paragraph, and bearing in mind that I'm not advocating it in every case, or even in many cases, what I'm observing about the behaviour of      - isOpaque and view clipping is not relying on some weird implementation detail, but a logical consequence of what must necessarily happen. The discussion of QD is not to be discounted altogether because (at present) you can use QD in a Cocoa view, and QD was originally designed to work without a backing store, so it assumes that no-one is going to be overwriting its pixels without telling it. Likewise, leaving QD aside, Cocoa's view system is surely not going to be writing pixels where it doesn't have to - we all know that any pixel pushing is usually far more performance-sapping than any amount of calculation of clipping paths, etc to avoid it. So if it sees a view that has set     - isOpaque it is bound to take advantage of that to avoid writing out those pixels! At least it better had be - I would like to think that Apple's code has been as super-optimised as possible (and experiments/experience confirm that this behaviour is reliably implemented).

In summary, I'm not suggesting this is a technique you should use a lot, but you can rely on it for some needs. I don't need to be reminded how a normal view is expected to be programmed, so please let's move on. Sometimes you can bend the rules when you know you can get away with it - it doesn't invalidate the usual rules, but it's another weapon you can bring to bear. You're the one taking this around in circles, since you seem determined to miss the point I'm trying to make. If you want to bring something new to the discussion, don't let me stop you, but please don't rehash the same argument - I get it already! 

----
Well, let me simply ask you this.

Let's say that the AppKit team were to change the internal implementation of the NSView machinery such that all invalidated areas of opaque NSViews were overwritten with the     goat se.cx image before each view's     drawRect: is invoked. What, if any, change to the documentation would the team then need to make in order to reflect this change?

----
Looking at this another way, the fact that the drawing system asks your view to redraw is, in itself, proof that your view may need to redraw. If there was absolutely no way that your view would need to redraw, the system wouldn't ask you to redraw it.

Also, if it sounded like I was advocating *always* drawing to an image first, I have miscommunicated. The only time an image buffer should be used is when you *must* make incremental changes to the previous drawing (e.g. "How can I just add new points instead of redrawing all of them with for statement?"). If you can redraw your view from scratch, then of course drawing should be done directly. This is how     NSCopyBits() is usually used. Any time you want to do incremental drawing, you send     -lockFocusIfCanDraw to make sure that you have an existing buffer. If you do, you can safely make your incremental changes; if you don't, you send     -setNeedsDisplay:. The key point is that if the buffer is destroyed at anytime,     -drawRect: is able to entirely recreate it.

