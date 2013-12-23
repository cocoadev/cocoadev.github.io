---
layout: page
title: CustomViewDrawsGarbageOutsideRect
---



When Drawing a custom view in my App, scrolling the view causes artifacts to extend beyond the view rect. http://goo.gl/OeSCu
A similar issue is reported on CompositingVsDrawing. (Maybe that question could be moved to this page, as it isn't really topical to compositing.)
The difference in my case is that my custom view is enclosed in a scroll view.

----

I managed to solve this one by inserting     lockFocus around my drawing code, like so:
 
    
- (void)drawRect:(NSRect)rect
{
     self superview] lockFocus];
     //... draw custom view here
     [[self superview] unlockFocus];
}


Just thought I'd put it up here as I couldn't find a reference to this solution anywhere else. (Though it makes perfect sense...)
I have put a link on [[CompositingVsDrawing to this page.

Thanks - Alx23

----

This looks like a pretty poor factoring that will break outside of the specific situation it's used in. The lockFocus should not be done inside the drawRect: method. You should do it before calling drawRect:, preferably by calling a wrapper method (like     display).

----

Do you mean I should have a     display method within my custom view code? And that this should call     drawRect after locking focus on the superview? Or outside my view class altogether? 

Ah, I think i see now. if i want to draw it in another custom view that has no superview, it will break. Thanks! -Alx23

----

I'm pretty sure drawRect: is called automatically by another method, probably     display, in NSView's default implementation. The correct code is not     self superview] lockFocus];, it's     [self lockFocus];. However, the     display method (or whatever) locks the focus for you, so as long as you remember to call     super for all overriden methods, you should be OK without manually locking the focus at all. This brings you back to square one, though, and I don't know what the solution to your original problem is. (I thought the [[NSClipView in the scroll view was supposed to stop this from happening). You could look at http://developer.apple.com/documentation/Cocoa/Conceptual/DrawViews/index.html if you haven't already... --JediKnil

----

NSView already responds to     display. This (or     setNeedsDisplay:) is the method you should usually use to tell a view to draw itself. These methods allow the view to perform all the setup and such that it needs without you having to worry about it. Calling     drawRect: directly isn't something you should generally be doing. If you are going to call it, you should have     lockFocus and     unlockFocus in the method that calls     drawRect:, not in     drawRect: itself. Putting it in your drawing code ties the drawing too directly to the view hierarchy, so it's not as flexible.

For an example of a case where it would break your drawing to have     lockFocus inside     drawRect:, consider DragAndDrop. You generally want to provide a picture of your object to be displayed under the cursor while the user is dragging. One very common way to do this is to LockFocus on an NSImage and call your view's drawing code (usually with some kind of switch to tell it you just want certain elements).

----

Thanks for the input guys. I'm pretty new to Cocoa and extremely new at the NSView API, so thanks for your patience. I think the problem stems from the fact that I have designed my project based on mmalc's GraphicsBindings example, which is based on KVO notifications. There is actually no code that calls     display anywhere in his example or my rehash of it. The closest calls are     [self setNeedsDisplay:YES]; called in     - (void)bind:...,      - (void)unbind:... and     - (void observeValueForKeyPath:... within the GraphicsView class. Subsequently my scroll view must be calling super's     display and something is going wrong there. This is my best guess. I'm going to keep experimenting, if anyone has any more ideas, I'd be very grateful. - Alx23

----

NSView's     setNeedsDisplay: method creates a timer that will cause the view to display (through a call to     display, I think) in the next run-through of the event loop. This is generally the best way to tell a view to draw itself, because it lets Cocoa handle pretty much everything (except the actual drawing, of course) for you. It shouldn't be causing any problems unless you've overridden one of the focusing methods or     drawRect: to do something funny.

Also, I don't think NSScrollView has a specialized     display method, so it shouldn't make any difference whether it called the method as     [self display] or     [super display].

----

Well, I went back and compared my code to mmalc's and of course it was my fault. I seem to have cleared up that problem at least by adding back the     [clipRect addClip]; which I had taken out for some reason. I guess my     [drawRect needs it in for the scroll view. Apologies for wasting anyone's time with this. I appreciate the advice - I intend to implement drag and drop and would probably have spent a whole lotta time debugging without it!
Thanks again - certainly reinforces my good opinion of CocoaDev. -Alx23

----

Okay, this is weird... The problem recurred almost immediately after I posted the above, and I've been tinkering ever since. It seems it's not the clip view at all. I got the idea it might be in the way I was drawing the graphic elements, so I put in an NSLog to check what the view's bounds NSMaxY value was evaluating to as I scroll. And whaddya know, the problem vanished!

Any one here have any ideas why this would be so? -Alx23

----

If it doesn't reappear after you remove the NSLog, there was probably an old object file lying around that got linked in by accident. Otherwise, it sounds like a HeisenBug.

----

Thankfully not a HeisenBug - in the end I just deleted my old build folder and rebuilt the executable - and THAT'S what did the trick. So probably an old object lying around like you said. Thanks again for all your help. -Alx23

