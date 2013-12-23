---
layout: page
title: CursorFlashingProblems
---



Q: Does anyone know how to prevent the cursor from flashing slightly everytime it is set?

A: It seems fixed for me in Jaguar and with Quartz Extreme. 

Q: How can I make my cursor stay set during a custom drag operation?

A: Call     -[NSWindow disableCursorRects] on your window when your drag begins and     -[NSWindow enableCursorRects] when it ends.

Q: How can I set a custom cursor for my split view?

A: Override     -[NSView resetCursorRects] like usual.

Discussion:
----

I've been going crazy trying to get a cursor that is set, to stay set, while a custom drag operation is under way. I've been using an NSTimer and calculating the mouse down delta offset using [myWindow mouseLocationOutsideOfEventStream]. I usually do this to drag objects around on the screen, but I'll also use this method to modify the frames of subviews. Sometimes you have to be aware that, when manipulating the frame of an NSScrollView with the mouse, the NSScrollView pushes a default document cursor onto the cursor stack. This happens whenever the frame is resized. The only way to keep your custom cursor on top off the cursor stack is to set the document cursor to your custom cursor for the duration of the custom drag operation.

    
-(void)mouseDown:(NSEvent *)theEvent {
   [timerObject start];
   [myScrollView setDocumentCursor:myCustomCursor];
}
-(void)mouseUp:(NSEvent *)theEvent {
    [timerObject stop];
    [myScrollView setDocumentCursor:[NSCursor arrowCursor]];
}


This was causing the custom cursor to flash while the scroll view was being dynamically resized. This could be the cause of the flashing mentioned in the original post. My guess is a resizing or scrolling operation on a scroll view could involve the scroll view pushing the document cursor onto the cursor stack. Just something to keep in mind when mixing cursor operations with scroll view operations. 

Hope this spares someone from going through hours of hacking around this conflict. --zootbobbalu

----

I was having this problem (cursor flicker) when dragging a divider in a subclass of NSSplitView.  I couldn't figure out how to apply zoot's fix to my situation, but I (accidentally) came up with a really weird remedy.  The cursor flicker seems to depend on the cursor itself!
 
The cursor you get like this:
    
NSCursor *cursor1 = [NSCursor _verticalResizeCursor];

or 
    
NSCursor *cursor2 = [[NSCursor alloc] initWithImage:[NSImage imageNamed:@"NSTruthVResizeCursor"]
                                            hotSpot:NSMakePoint(8,8)];

(visually the same cursor) will flicker if used when dragging a divider, but this one
    
NSCursor *cursor3 = [[NSCursor alloc] initWithImage:[NSImage imageNamed:@"NSTruthVerticalResizeCursor"]
                                            hotSpot:NSMakePoint(8,8)];

doesn't!  I haven't been able to figure out why (but I'm happy!).  In cursor2 and cursor3 the (cursor's image's) image representation is an NSBitmapImageRep while in cursor3 it is NSCachedImageRep, so I thought that would be it.  However, if I do this
    
NSImage *cursorImage = [NSImage imageNamed:@"NSTruthVResizeCursor"];
[cursorImage lockFocus];
[cursorImage unlockFocus];
NSCursor *cursor4 = [[NSCursor alloc] initWithImage:cursorImage
                                            hotSpot:NSMakePoint(8,8)];

Then I get a cursor that uses an NSCachedImageRep and yet still flickers.  Huh.  I'm interested if anyone else can unravel the situation.

By the way, at least in Jaguar cursor3 is the cursor used in NSSplitView for divider dragging.  Oh, and I'm following this document http://developer.apple.com/documentation/Cocoa/Conceptual/DrawImages/Tasks/CachingRepresentations.html to get my image representation to cache.

-KenFerry

----

How is your window buffered? --zootbobbalu

----
A follow-on question: There doesn't seem to be an easy-to-find NSSplitView method for setting the cursor. I have a splitview frame that can only be collapsed or completely "open" and want to swtich between     resizeRightCursor and     resizeLeftCursor accordingly. NSSplitView is obviously setting the "mouse over divider" cursor but I'm not finding any way to tell it which cursor to use (or which method it's setting the cursor in so I can override it). Is there a     method for doing this built in? Something I should override? Does anyone know where the correct approach for doing this is documented? Thanks in advance.

