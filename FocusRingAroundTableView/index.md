---
layout: page
title: FocusRingAroundTableView
---

**At least with Panther, it would seem that the NSTableView draw its own FocusRing � so perhaps this page is only useful for pre-Panther development?**
----
Here is a way to draw a focus ring around an NSTableView when it is the FirstResponder:

Create a subclass of NSScrollView:

    
@interface FocusRingScrollView : NSScrollView 
{
    BOOL shouldDrawFocusRing;
    NSResponder* lastResp;
}
@end

@implementation FocusRingScrollView

- (BOOL)needsDisplay;
{
    NSResponder* resp = nil;

    if ( self window] isKeyWindow] ) 
    {
        resp = [[self window] firstResponder];
        if (resp == lastResp) 
            return [super needsDisplay];
    } 
    else if ( lastResp == nil )  
    {
        return [super needsDisplay];
    }
    shouldDrawFocusRing = (resp != nil && [resp isKindOfClass:[[[NSView class]] 
                            && [(NSView*)resp isDescendantOf:self]); 
    lastResp = resp;
    [self setKeyboardFocusRingNeedsDisplayInRect:[self bounds]];
    return YES;
}


- (void)drawRect:(NSRect)rect 
{
    [super drawRect:rect];

    if ( shouldDrawFocusRing ) 
    {
        NSSetFocusRingStyle(NSFocusRingOnly);
        NSRectFill(rect);
    }
}

@end


In InterfaceBuilder, also subclass NSScrollView as FocusRingScrollView.  Then on the inspector panel for the scroll view containing the table in question, set the Custom Class to FocusRingScrollView.

Many thanks to Nicholas Riley for his help with this.

-- StevenFrank

----


Here's a fix for the drawRect method:

    
- (void) drawRect:(NSRect)rect
{
    [super drawRect:rect];
    if(shouldDrawFocusRing) {
        [self setKeyboardFocusRingNeedsDisplayInRect:[self bounds]];
        NSSetFocusRingStyle(NSFocusRingOnly);
        NSRectFill([self bounds]);
    }
}


Otherwise,  once it has been drawn a few times, the focus ring drawing gets corrupted at some places.

----

The above "fix" does not work; it causes the focus rect to be drawn way too often, killing performance (even on a GeeFour/800).  I've not had problems with the first method, and it's in use in several apps of mine. -- NicholasRiley

----
You should NOT be calling set...NeedsDisplay...: in a drawRect: method! -Sean
----

I have a different, but essential, fix to drawRect.  The focus ring is drawn for the passed-in rect parameter, but this is not necessarily the bounding rect of the scroll view - it can be just the rect that needs updating.  I've seen the edge of the focus ring draw through the middle of an outline view for this reason (when only some columns need to be redrawn).  The fix is just to get the view's bounds and ignore the passed-in rect.

    
- (void)drawRect:(NSRect)rect 
{
    [super drawRect:rect];

    if ( shouldDrawFocusRing ) 
    {
        NSSetFocusRingStyle(NSFocusRingOnly);
        NSRectFill([self bounds]);
    }
}


- Christopher Corbell

----
Yet another small glitch is corrected by overriding both setFrameOrigin and setFrameSize. setFrame calls those two in fact. If you place the view in a NSSplitView (bottom or right) and you move the down (or right) the split while the focused view has the focus, you will see trailing marks...

    
- (void)setFrameOrigin:(NSPoint)origin
{
    if (shouldDrawFocusRing) {
    
        [self setKeyboardFocusRingNeedsDisplayInRect:[self bounds]];
    }
    
    [super setFrameOrigin:origin];
}

- (void)setFrameSize:(NSSize)size
{
    if (shouldDrawFocusRing) {
    
        [self setKeyboardFocusRingNeedsDisplayInRect:[self bounds]];
    }
    
    [super setFrameSize:size];
}


- Eric

----
As it says above, the NSTableView now draws the focus ring itself, but say for your own custom view the above techniques are useful, but you should be cafeful to save the graphics context, like so:
    
[NSGraphicsContext saveGraphicsState];
NSSetFocusRingStyle (NSFocusRingOnly);
[[NSBezierPath bezierPathWithRect:[self bounds]] fill];
[NSGraphicsContext restoreGraphicsState];

-Sean

----
I have a reversed situation. I need never focused my TableView. How can I do it?

