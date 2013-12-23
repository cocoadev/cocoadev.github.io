---
layout: page
title: CustomNSSplitViewDivider
---

Can anyone direct me to what I need to know in order to draw a custom NSSplitView divider, ala Mail or iTunes 5 browser. I have been looking at drawDividerInRect, but I can't seem to get it working. Sample code would be much appreciated, just to give me and idea of where to start.

*Look at the NSSplitView page, particularly the alternatives section. This doesn't directly tell you "how," but it will solve the problem.*

----

Do you have to use an alternative in order to do this? I mean, Apple doesn't for Mail, Xcode, etc. do they? Or is it just that much harder to use NSSplitView?

----

Ummmmm why would you ever need to use a complete replacement just to modify the drawing behavior???  Just override the method you mentioned, it works fine -- I've done it in the past with excellent results.

*Well, if you're subclassing anyway, why not take more features? It's almost always better to use someone else's code than to spend time creating your own. --JediKnil*

That's fine if that code does 100% of what you need and is bug free. Once you have to start extending/fixing that code, the quality of that code is going to matter a whole lot.

----

Thanks, I have it working perfectally now with NSSplitView. I was making it too hard for myself when the answer was staring me right in the face.

----

Can anyone show me how I can draw a custom divider like the one in Mail (the horizontal, not the vertical). Many examples include: NewsMac Pro, Mail, XCode. I want the good looking divider that Apple creates, not what they give us. And please post code if possible thanks!

*Override -[NSSplitView drawDividerInRect:], draw whatever you want.*

Yes, I understand that but I am not very proficient at drawing. Can you show me an example?

This is what I did and it is not working:

    
- (void)drawDividerInRect:(NSRect)aRect
{	
	[[NSColor clearColor] set];
	
	NSImage *image = [NSImage imageNamed:@"vertical_splitview"];
	[image setSize:aRect.size];
	[image compositeToPoint:aRect.origin
				   fromRect:aRect
				  operation:NSCompositeSourceOver];
}


----

*For one thing, it's not "clear" why you're setting clearColor and not using it. For another thing, you're not locking focus on your view when drawing. I'm not sure what else may be wrong, though.*

*This is what I use ... It's probably a bit verbose but it works just fine. For future reference, please see HowToAskQuestions - "it's not working" is not a good way of eliciting help. Describing the actual symptom is. ;-)*

    

- (void)drawDividerInRect:(NSRect)aRect
{
	// Create a canvas
	NSImage * canvas = [[[NSImage alloc] initWithSize:aRect.size] autorelease];
	
	// Draw bar and grip onto the canvas
	NSRect canvasRect = NSMakeRect(0, 0, [canvas size].width, [canvas size].height);
	NSRect gripRect = canvasRect;
	gripRect.origin.x = (NSMidX(aRect) - ([grip size].width/2));
	[canvas lockFocus];
	[bar setSize:aRect.size];
	[bar drawInRect:canvasRect fromRect:canvasRect operation:NSCompositeSourceOver fraction:1.0];
	[grip drawInRect:gripRect fromRect:canvasRect operation:NSCompositeSourceOver fraction:1.0];
	[canvas unlockFocus];
	
	// Draw canvas to divider bar
	[self lockFocus];
	[canvas drawInRect:aRect fromRect:canvasRect operation:NSCompositeSourceOver fraction:1.0];
	[self unlockFocus];
}



*    bar and     grip are NSImages I cache when the  divider is initialized. I don't know if this adds any extra performance (versus using     -imageNamed: each time     -drawRect: is called), but I do it anyway. :-)*

----

I am curious as to why you focused and drew into the intermediate image     canvas.  It should work fine drawing directly into     self.  Is there some gotcha that I don't know about?

----
I can't remember why I did this, but how about you try it without the intermediate image and let me know how it works for you? :-)
----
It works fine without the intermediate step.

----

What about if it is a horizontal bar? This only works for vertical ones.


----

 When determining the canvas metrics, you can ask     [self isVertical]; and determine use height or width accordingly. From there you can either a) rotate the image or b) use another pre-rendered image (the first choice is probably the better one - I believe it's less overhead than finding and loading another image).

