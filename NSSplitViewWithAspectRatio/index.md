---
layout: page
title: NSSplitViewWithAspectRatio
---



I'm trying to create a subclassed NSSplitView for a specific need: I want a 2-subview split with one of them always keeping a specific aspect ratio (in this case, 2.5 times its width). To that end I've subclassed NSSplitView, and I've determined that I really only need to override adjustSubviews as follows

 -(void)adjustSubviews
 {
    NSLog(@"adjust splitviews");
    id left=self subviews] objectAtIndex:0];
    id right=[[self subviews] objectAtIndex:1];
    float width;
    
    // determine if user resized the divider directly, or if this was
    // from another event such as a window resize with a "springy" splitview
    if([[NSMouseInRect([NSEvent mouseLocation], [self bounds], NO))
    {
        // divider resize, calculate based on new width
        width=[right frame].size.width;
    }
    else
    {
        // from elsewhere, calculate based on new height
        width=round([self frame].size.height/2.5);
    }
    
    // resize the subview to the correct aspect, and resize the splitview for new height
    [left setFrameSize:NSMakeSize([self frame].size.width-width-[self dividerThickness],round(width * 2.5))];
    [self setFrameSize:NSMakeSize([self frame].size.width,round(width * 2.5))];
    [super adjustSubviews];
 }

I have this subclassed splitview in a window, set to expand with the window ("springy"); it works perfectly when I resize the window, but it doesn't work when I try to resize the splitview directly using the divider. The problem is that I don't know how to capture a divider resize/move (adjustSubviews doesn't fire). I've also tried using a notification (no go), as well as overriding mouseDragged: but it seems that only fires when the mouse is dragged in one of the subviews, not the divider.

Is there a way I can detect a divider move?

----

Instead of subclassing NSSplitView, set an object as its delegate and implement the delegate methods. See SplitViewBasics.

I've already tried this; it works, but the code is really messy, mostly due to the fact that I must disable/reenable notifications and delegates (for the splitview, windows, etc.) so that resizes work properly and isn't jittery due to the resize codes being called twice (from delegate + notifications), and I still haven't been able to really make that work perfectly (some notifications still occur dispite being removed), so I really think subclassing is a better solution in end, both for control of the resize, code size, and just plain simplicity.

----

I think you had your [super adjustSubviews] for nothing there, and the splitview tended to move up within the window when resizing the window. 
I did have a similar need a while back; here's what I came up with (adapted your code above to keep it familiar to better see changes):

 @interface RatioSplitView : NSSplitView {
    BOOL selfDrag;
 }
 -(id)init;
 @end
 
 @implementation RatioSplitView
 -(id)init
 {
    self=[super init];
    selfDrag=NO;
    return self;
 }
 
 
 -(void)adjustSubviews
 {
    id right=self subviews] objectAtIndex:0];
    id left=[[self subviews] objectAtIndex:1];
    float width;
    float height;
    
    [[NSEvent * theEvent=[NSApp currentEvent];
    if( [self frame].size.height!=round([left frame].size.width * 2.5) || [theEvent deltaX] || [theEvent deltaY])
    {
        if(!selfDrag) // dragging divider
        {
            height=[self frame].size.height;
            width=round(height/2.5);
        }
        else  // other resize
        {
            width=[left frame].size.width;
            height=round(width * 2.5);
        }
        // resize the subview to the correct aspect, and resize the splitview for new height
        [right setFrameSize:NSMakeSize([self frame].size.width-width-[self dividerThickness],height)];
        [left setFrame:NSMakeRect([self frame].size.width-width,[right frame].origin.y,width,height)];
        [self setFrame:NSMakeRect([self frame].origin.x,[self frame].origin.y,[self frame].size.width,height)];
    }
    else
    {[super adjustSubviews];}
 }
 
 - (void)mouseDown:(NSEvent *)theEvent
 {
     BOOL keepOn = YES;
     BOOL isInside = YES;
     NSPoint mouseLoc;
     while (keepOn) {
         theEvent = self window] nextEventMatchingMask: [[NSLeftMouseUpMask | NSLeftMouseDraggedMask];
         mouseLoc = [self convertPoint:[theEvent locationInWindow] fromView:nil];
         isInside = [self mouse:mouseLoc inRect:[self bounds]];
         switch ([theEvent type]) {
            
        case NSLeftMouseDragged:
            selfDrag=YES;
            id rightView=self subviews] objectAtIndex:0];
            id leftView=[[self subviews] objectAtIndex:1];
                
            [[NSSize rightSize=[rightView frame].size;
            NSSize leftSize=[leftView frame].size;
            leftSize.width-=[theEvent deltaX];
            rightSize.width+=[theEvent deltaX];
 
            leftView superview] setNeedsDisplayInRect:[leftView frame;
            rightView superview] setNeedsDisplayInRect:[rightView frame;
            [leftView setFrameSize:leftSize];
            [rightView setFrameSize:leftSize];
            self superview] setNeedsDisplayInRect:[self frame;
                
            [self setNeedsDisplay:YES];
            break;
            
            
        case NSLeftMouseUp:
            selfDrag=NO;
            keepOn = NO;
            break;
             
            
        default:
            // Ignore any other kind of event. 
            break;
         }
     };
     return;
 }
 
 @end

It's smooth when resizing the window, and the splitview itself, but it could probably be optimized (don't know if I have the setNeedsDisplay... stuff correctly in the right order). I didn't try to see if the window could be resized automatically when the spitview is resized by hand, but I suspect it might cause a loop since the splitview would get resized too I imagine (and round and round we go).

But I agree - beats trying to implement delegates and such to make the resize happen.

-Seb

