---
layout: page
title: DraggingNSViews
---



Hey everyone, I have a question for the community. I'm trying to drag an NSView around a window. I have it working 80% correctly, but there is a problem. While dragging, a negative set of coordinates is sent, and the view jitters all over the place. Here's the code I have (it is mainly adapted from Sketch code): http://goo.gl/OeSCu

    
- (void)drag: (NSEvent *)inEvent;
{
	NSPoint	theLastPoint	= NSZeroPoint;
	NSPoint	theCurrentPoint	= NSZeroPoint;
	BOOL	theMovingFlag	= NO;
	float	theXDelta	= 0;
	float	theYDelta	= 0;
	
	theLastPoint = [self convertPoint: [inEvent locationInWindow] fromView: nil];
	_startPoint = [self frame].origin;
	
	while( 1 ) {
		inEvent = self window] nextEventMatchingMask: ([[NSLeftMouseDraggedMask | NSLeftMouseUpMask)];
		theCurrentPoint = [self convertPoint: [inEvent locationInWindow] fromView: nil];
		
		theXDelta = (theCurrentPoint.x - theLastPoint.x);
		theYDelta = (theCurrentPoint.y - theLastPoint.y);
		
		if( (! theMovingFlag) && ((fabs(theXDelta) >= 2.0) || (fabs(theYDelta) >= 2.0)) ) {
			theMovingFlag = YES;
			
			[self startFrameManipulation];
		}
		
		if( theMovingFlag ) {
			if( ! NSEqualPoints(theLastPoint, theCurrentPoint) ) {
				[self invalidate];
				
				[self moveBy: NSMakePoint(theXDelta, theYDelta)];
				
				[self invalidate];
			}
			
			theLastPoint = theCurrentPoint;
		}
		
		if( [inEvent type] == NSLeftMouseUp ) {
			break;
		}
	}
	
	_dropPoint = theLastPoint;
	
	if( theMovingFlag ) {
		[self stopFrameManipulation];
	}
}

- (void)moveBy: (NSPoint)inVector
{
	[self setFrame: NSOffsetRect([self frame], inVector.x + 1, inVector.y + 1)];
}

- (void)startFrameManipulation
{
	_flags.manipulatingFrame = YES;
	_originalFrame = [self frame];
}

- (void)stopFrameManipulation
{
	if( _flags.manipulatingFrame ) {
		NSRect	theFrame = NSZeroRect;
		
		theFrame = [self frame];
		
		if( ! NSEqualRects(_originalFrame, theFrame) ) {
			NSRect	temp;
			
			_flags.manipulatingFrame = NO;
			temp = theFrame;
			[self setFrame: temp];
		} else {
			_flags.manipulatingFrame = NO;
		}
	}
}


As I said earlier, this all works fine, except every time moveBy: is called, it is called again, but with a negative X coordinate. It's really weird. Does anyone have any ideas? (and I cleaned up the code for posting; my style doesn't work well with posting here :)) Thanks in advance, QuentinHill

----
How about just overriding -mouseDragged like so...

    
- (void)mouseDragged:(NSEvent*)event
{
    NSRect rect = [self frame];
    rect.origin.x += [event deltaX];
    rect.origin.y -= [event deltaY];
    
    [self setFrame:rect];
    self superview] setNeedsDisplay:YES];
    
    [super mouseDragged:event];
}


--[[NeilVanNote

----

I think your problem is that you're calling -nextEventMatchingMask: in a tight loop but you're not checking if it's returning nil, so you're getting garbage from -locationInWindow when it is.  I'd have to second Neil's advice, and add that you should move your -startFrameManipulation to your mouseDown: event handler and -stopFrameManipulation to your -mouseUp: event handler.  -- Bo

----

Because my method uses more lines of code :P Seems I completely overlooked <code>-mouseDragged</code> and <code>-mouseUp</code>. That did the trick. Much abliged to both Bo and NeilVanNote -- QuentinHill

----

A new version to account for a superview with a flipped coordinate system and to only mark "where we were" and "where we went" as needing an update.

    
- (void)mouseDragged:(NSEvent*)event
{
    NSView *superView = [self superview];
    NSRect frameRect  = [self frame];
    NSRect rect = NSOffsetRect(frameRect, [event deltaX], [superView isFlipped] ? [event deltaY] : -[event deltaY]);
    [self setFrame:rect];
    [superView setNeedsDisplayInRect:NSUnionRect(frameRect, rect)];
    [super mouseDragged:event];
}


--NeilVanNote

Added note: that NSUnionRect might be very inefficient - it causes a potentially huge rect to be updated. Instead, just invalidate the frame before and after the move, so two separate rectangles are invalidated. The system will coalesce them into a complex region for you, so keeping the update area to a minimum. -GrahamCox

----

This works, too:

    
- (void)mouseDragged:(NSEvent*)event
{
	NSPoint origin = [self frame].origin;
	origin.x += [theEvent deltaX];
	origin.y += (self superview] isFlipped] ? [theEvent deltaY] : -[theEvent deltaY]);
	[self setFrameOrigin:origin];
}


--jrc

----

** "Snap Drag'in" **

How can this be accomplished 'live'? In other words, I want the view to smoothly follow the mouse while in a drag. (Nov 18 '05)

(Dec 1, '05) Let me clarify this a bit more. I have a 'grip' graphic in the lower-right of my custom view that allows the user to horizontally resize the view by dragging the grip. When the mouse is moved in a drag (using the method above), if you move too fast, the mouse pointer ends up outside the grip area and stays there; when you stop moving the mouse (while still in drag), the pointer is still outside the grip area defined by my tracking rect (in other words, the view doesn't properly track the mouse pointer). I'm recreating Mail 2.x's mailboxes view (the bottom glossy bar's grip region). How can I get the view's tracking rect to 'snap to' the pointer during the drag?

----
*Why don't you check where the mouse ends up and then adjust the grip. Just convert     [[[NSEvent mouseLocation] to window coords.* -JeremyJurksztowicz

----
I'd thought of that, but I guess what I'm asking is how. I admit I only have a vague understanding of how the above method actually works. At what point would I check that for adjustment?

... update: Ah - I see exactly what you mean now. Rather than "    myWidth += [event deltaX];" I want to get the actual mouse location and set the width to that (minus a small offset to center the pointer over the grip rect). I've made this adjustment and it works beautifully. Thank you for the (what I now see is a fairly obvious) suggestion, Jeremy. 

----

I'm kind of at that awkward position of knowing enough ObjC/Cocoa to get myself in trouble and not enough to  avoid it. 
I'm just about ready to start implementing drag and drop in a custom tool but am not sure which way to approach it.
Here's what I'm trying to get:

http://www.adaptiveview.com/img/temp.gif

(These are screenshots I did of the actual tool with the mouse pointer, drag image and highlighting added manually so I 
could get a visual image of what I wanted. The left/right segmented control to the far left is related to a different part of 
the GUI, not a part of the tool discussed here.)
----

The tool is used to change the order of a set of colors. The picture above shows 4 views of what I'd like
the drag/drop process to *look* like. In the top 2, the color on the left end is being dragged to the right. If it's positioned
between 2 colors this means the user wants to move the color so its between these colors. If it's positioned on top of
another color then the user wants to swap the positions of the two colors. Note the highlighting of both the dragged-from
and dragged-over colors (intended to indicate that this is a swap, not a copy of one color to another). The bottom 2 pictures
show the same thing but this time the far right color is being dragged to the left.

The color "cells" are subclasses of NSImageView, each occupying a fixed position within a containing NSView (not visible),
which is also subclassed. My question is, where should the drag/drop code be implemented? It looks to me like it needs
to be in the containing view's methods. This would allow it to draw the vertical "insert" highlighting between the cells. Since
the cells are each in a fixed position within the containing view (and don't actually change postion, or size) the containing 
view should have all the info it needs to draw the highlighting and control the drag/drop.

Is this the "right" way, or should I be looking in a different direction here? Anything else along the lines of "Whatever you do, don't ..."
and "Make sure you ..." would also be appreciated.

----

The drag & drop code can (and probably should) be in the control cell code - you just need a pointer to the superview to draw in it.

    

-(void)drawHighlightAtPosition:(NSRectEdge)edge
{
    NSView *superView = [self superview];

    [superView lockFocus];
    // draw here in the appropriate place
    [superView unlockFocus];
}


----

Why "... probably should"? Excuse me if I come off as being argumentative -- I'm just trying to understand. Take the case of dragging the far-right color. I get that the cell code could,
in effect, say: if the mouse pointer's been dragged to my left then tell my superview to draw a highlight line between me and the next cell. 
The question, then, is what happens when the mouse pointer gets over
the next cell to the left (or anywhere left of the next cell to the left). 
If I've got the mechanics right, the mouse-dragged event message is going to be sent to the "cell" instance where the drag operation started. So does it keep sending "draw highlight" messages to its superview and then the superview sorts out where exactly the pointer is and therefore where (and if) a highlight gets drawn? I guess what this approach looks like to me is that it requires either the individual cells to "know" things outside of their domain (e.g., where the other cells are located) or you've got cells telling their superview what to do and the superview sorting things out and figuring out what should actually be done. This is why I thought the superview should be handling the drag/drop. 

----

What I'd do is make the image wells (wouldn't NSColorWell be a better fit?) contained within a special purpose view that would handle the mouse tracking & drawing (and any other interaction between the individual wells.) Make this view a subview of your 'master' view.

Or how about just having a modifier key indicate swap vs. change order? Option-drop to swap, Plain drop to change order (Cmd-Option drop if you're also allowing replacing the wells' contents via drop)

----
I think what you're suggesting is what I was thinking should be the approach -- what you call "a special purpose view" is what I've called the "containing view." As to why not color wells, because they have a very specific function and behavior which isn't in line with how this particular control works. I had to come up with something that fit in the available space, looked conceptually close to color swatches but far enough away from color wells that users wouldn't be expecting them to accept dropped colors. The image views ended up looking good enough that I didn't see any need to draw my own controls. As for using modifier keys, if I can get away without having to, I will. Call that a personal preference -- I really don't like having to reach for the keyboard to control a "mouse driven action."

