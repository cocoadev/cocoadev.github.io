---
layout: page
title: HowToDrawToAViewOutsideOfDrawRect
---



While in an NSView subclass, how do you draw to a view created on-the-fly, and outside of the drawRect: method of the original subclass? Is this even possible? I'm trying to avoid subclassing at all costs. 

The code below is called whenever I need to generate a background (whenever a pref is changed). The drawing is then stored as an image for the drawRect: method to draw with dissolveToPoint:fraction: .  http://goo.gl/OeSCu
    
- (void)createBgImage
{


    // Other drawing code here...


    // I'm storing all of the previous drawing inside an image
    imageData = [self dataWithPDFInsideRect: [self bounds]];
    mainBgImage = [[NSImage alloc] initWithData: imageData];
    
    [imageData release]; 
}


----

If you want to draw into a view outside of its -drawRect: call, you need to call -lockFocus on the view beforehand and -unlockFocus afterwards.  -- Bo

----

Isn't it bad practice to draw to a view outside of its drawRect: method? Why not set some variable or other to tell it what to draw, and then call setNeedsDisplay:?

What reason do you have to avoid subclassing at all costs?

----

No reason really, its just that I think it'd be much more neat.
- JohnDevor

----

Going against the proper way of doing things is not neat.

----

I think he means that the code would be neater, and more manageable.

----

My first custom view did all its drawing outside of drawRect:, because I didn't understand how views were supposed to work. It caused some problems because it wouldn't keep the image after being minimized, or in a few other circumstances which I forget. To avoid these problems, it is necessary to do drawing in drawRect:, and if the code to draw is in there, there's no point in duplicating it somewhere else. I've worked on other software in my job where some drawing is done where it should be and some isn't, and it's a pain when something needs to be changed to draw a different way and I fix it in the normal drawing routine but there is still some code elsewhere which is drawing it the wrong way. It's hard to find the drawing code which is in the wrong place.

----

To understand if drawing outside of drawRect: is right or wrong, it helps to understand what drawRect: does for you. I know what you're thinking: "That's easy! AppKit calls my view's "drawRect:" method when it's time for the view to draw." Uh huh. When is it time for your view to draw? 


* Maybe your window just appeared on screen the first time; or,
* Maybe something happened in the system that caused your window's backing store to invalidate; or,
* Maybe the superview (or subview) was just forced to redraw this invalidating this view's area; or, 
* Maybe this view was just deliberately invalidated by the programmer. (i.e., forced to redraw because some underlying state changed); or,
* Maybe others, I don't really know ... and that's exactly my point!


It's not too hard to figure out when the window appears on screen that first time -- or at least you can figure out when you *think* it's going to appear. And you know when your own underlying state changed. But the other cases are just too far out of your control to get a handle on. You have no choice but to trust the system.

And not so fast! Your answer was only half-right. The other thing drawRect: does for you is help coalesce redundant copy requests. Say you have a view that displays 3 different pieces of data: a file name, a time stamp, and an icon. You want to make sure the view re-draws when any one of those things change. But what happens when all three of those things change at once? Do you want to redraw your view three times? No, you want to redraw it just once. Are you going to redraw it three times anyway? Depends on how smart you are I guess. Me? I take the easy way out and just get drawRect: to do all this for me.

So here is where you say "Well if it does so much for me, how am I supposed to get my stuff to draw?" And I reply "SRTD: Simply Read The Documentation." But don't get mad -- the best way to get good at how these Cocoa objects actually work is to pour over the docs and let it all sink in. In the meantime let's see how drawRect: actually works.

The idea here is that NSView is responsible for drawing itself. Thus, the drawRect: method, see above. But NSView is not responsible for knowing when to draw, since that decision is often delegated to model-level or controller-level code (what does this mean? Review ModelViewController.). NSView provides several ways to trigger a redraw (How many? SRTD!) but one of the most common  is:

    
- (void)setNeedsDisplay:(BOOL)flag;


This method marks a view as needing display (nor not) and schedules a redraw to happen at the end of this pass through the event loop (or not). AppKit will make sure the right number of subviews (and superviews) get redrawn as well to make sure your view's area looks exactly right. (How exactly? SRTD!). Since the re-draw happens at the end of this pass through the event loop, multiple redraw requests are coalesced into one. Consider the following pseudo code:

    
<set the file name>
[view setNeedsDisplay:YES];
<set the time stamp>
[view setNeedsDisplay:YES];
<set the icon>
[view setNeedsDisplay:YES];


Each one of these set/redraw tuples might be implemented in an accessor like so:

    
- (voidsetFileName:(NSString*)fileName
{
    if (fileName != mFileName) {
        [mFileName autorelease];
        mFileName = [fileName retain];
    }

    [delegate dataChangedForFileObject:self];
}


At the end of all three accessor calls, the view is still scheduled to be redrawn at the end of this pass through the event loop. This means this view, its subviews, and its superviews will only be drawn once, instead of three times.

So, it's clear drawRect: gives us a lot for free, and it's really not very difficult to use. Considering all that, there's generally no reason to do drawing outside of drawRect:. It's definitely not cleaner (the AppKit has to redraw the object anyway, why duplicate code?). It's definitely not easier (the AppKit knows all the special edge cases of when to redraw and how many objects to draw, why duplicate code I don't even understand?). It may be neat-as-in-fun, but it's definitely not neat-as-in-tidy. It doesn't make your code more manageable -- it makes the code even harder to maintain! And the benefits of avoiding subclassing at all costs (which are ... er ... not subclassing, I guess) don't really outweigh the fact that you must subclass in order to get AppKit drawing to work exactly right.

So, I'd say drawing outside of drawRect: is wrong, and should be avoided. Now that I have a better understanding of how NSView works, I only use drawRect: -- even when tracking in custom controls! SRTD!

-- MikeTrent

----

That's precisely the point I was trying to make, only much better said!

----

There actually are times when you want to draw outside of drawRect:. Do a Google search for "drawing outside of drawRect" and the top search result will be this Apple document:

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaViewsGuide/AdvancedSubclassing/chapter_6_section_3.html

Which gives a valid example of a time you might want to draw outside of drawRect:. This can come up many times when you are in a modal mouse tracking loop in a mouseDown: method and you need to draw during the loop. You either lock focus and draw there (or call a method to draw), or you force immediate display with the display method. Forcing immediate display would allow you to keep your hands clean and do no actually drawing outside of drawRect: (the system will do all the setup for you), but if you do that you are redrawing the entire view (and all its subviews) and that is not very efficient if all you need to do is repaint a tiny corner of the view. So in those cases you basically have to draw outside of drawRect:, or your drawing will be horrendously inefficicent.

Unfortunately, drawing outside of drawRect: is not so easy, as already pointed out, and there is a lot more to it than just calling lockFocus and unlockFocus, regrettably. I'll see if I can find a nice open source example of how it's down to paste in here.

----
There is actually no reason you can't just do a setNeedsDisplayInRect: in that situation just as you would in any other.

----
*I'm trying to avoid subclassing at all costs.* Why? It's not like subclassing really costs anything or is difficult, unlike NOT subclassing, which often has lots of costs and can be very difficult. What's your good reason?

