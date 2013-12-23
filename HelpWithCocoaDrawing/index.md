---
layout: page
title: HelpWithCocoaDrawing
---

 

I need some advice with Cocoa drawing. I have an NSView that is very complex. A single drawRect call involves hundreds of thousands of NSBezierPath calls. Here's my problem. I would like to have an independent graphic element move across the complex NSView drawing. How can I do this without having to redraw the complex NSView in the regions the independent graphics element is hovering over?

Should I just obtain an NSBitmapImageRep (BIR) of the visiable NSView region after the NSView has redrawn inside the visiable window area, and then cover the visiable NSView region with this BIR. This would allow me to flush small portions of a cached representation of the complex NSView when the independent graphic element leaves a dirty region in its wake. It seems like there has to be a better way to do this.


Help!!

--zootbobbalu

----

Yes you can do it that way, but maybe you should draw your 'hovering' graphic on a transparant/invisible window that lies on top of the view. This way you only need to redraw the transparent window, but not the document window(and the view). Windows are usually buffered.

-- JP

---- 

thanks JP,

I found three cool window methods that do exactly what I want.

-cacheImageInRect:
-restoreCachedImage
-discardCachedImage

The independent graphics element now drags seamlessly without any skipping.

I think I might try your method too to see which method requires more memory.

----

Now those three functions sound like they might be rather useful.

I vote this information not be orphaned! Do keep us posted on your developments, zoot - I for one would love to know how to do this kind of basic thing efficiently.

For reference, here's a brief overview page on the trio: http://developer.apple.com/techpubs/macosx/Cocoa/TasksAndConcepts/ProgrammingTopics/WinPanel/Tasks/CachingWindowImage.html

-- KritTer

----
thanks for the link KritTer!

I thought about it some and the transparent window on top of the document window might cause responder chain headaches when mouse/keyboard events are involved. Not sure about this though. I'll write up my findings sometime later today or tomorrow. 

--zootbobbalu

----

hey does anyone know how to perform boolean operations on NSRects. I would like to perform an XOR between two NSRects and then AND the XOR result with one of the rects. I guess this type of operation would envolve arrays of NSRects.

I'm going to begin slaving threw the process of writing a class that performs boolean operations on NSRects, so if someone can save me from this task I would appreciate it.


thanks,
--zootbobbalu

----

Cool, 

-cacheImageInRect:
-restoreCachedImage
-discardCachedImage

I somehow forgot those, what a shame.

I wonder how memory efficent is the NSWindow's caching.

--JP

----

OK, here's what worked for me:

    
-(void)mouseDown:(NSEvent *)event {
    [self mouseDragged:event];
}
-(void)mouseUp:(NSEvent *)event {
     NSWindow *window = [self window];

     // if you want to keep the drawing done in "updateMethod" then
     // you have to implement the same drawing routine in your "drawRect" method
     // eraseUpdateMethodDrawing is an instance variable to control if the "updateMethod"
     // drawing is left on the screen until the next screen redraw. 

    if (eraseUpdateMethodDrawing) {
        [window restoreCachedImage];
        [window flushWindow];
    }
    [window discardCachedImage];
}
-(void)updateMethod:(NSRect)updateRect {
    [[NSColor blueColor] set];
    [NSBezierPath fillRect:updateRect];
}
-(void)mouseDragged:(NSEvent *)event {
    NSWindow *window = [self window];
    NSPoint mousePoint = [self convertPoint:[event locationInWindow] fromView:nil];   
    NSRect updateRect = NSMakeRect(mousePoint.x-20, mousePoint.y-20, 40, 40);
    [window restoreCachedImage];
    [window discardCachedImage];
    [self autoscroll:event]; //if you want scrolling
    [window cacheImageInRect:[self convertRect:updateRect toView:nil]];
    [self lockFocus];
    [self updateMethod:updateRect];
    [self unlockFocus];
    [window flushWindow]; 	
}


-restoreCachedImage is called first because you want to start with the original backdrop that was cached the first time this method was called. The original backdrop was the content of the window before any drawing was performed by updateMethod. There is nothing to "restore" the first time, because nothing will be cached and nothing will be drawn until updateMethod is called for the first time.

-discardCachedImage needs to be called to avoid memory leaks. You don't have to worry about getting any signal errors when you ask a window to discard a cache that doesn't exist (thanks Apple!). I guess one extra line (to check if anything needs to be dicarded first before releasing a nonexistant cache) does not present too much overhead in this case (yeah!). 

-cacheImageInRect is called to recache the backdrop before drawing begins. 

The key thing to get right in all of this to to make sure that everthing that you draw inside the method "updateMethod" is only drawn inside the same NSRect that is used in the previous call to "cacheImageInRect:". In this case everything that is drawn here is drawn in the rect "updateRect" and the cached region is also the rect "updateRect".

Since the window is only updated via the flushWindow call it doesn't matter too much how long your updateMethod takes, though in practice it's probably best to keep it simple.
 
--zootbobbalu

Thanks for this! I've modified your code and explanation slightly as it was still calling drawRect all the time - this now seems to work reliably in 10.4.7

--RbrtPntn

