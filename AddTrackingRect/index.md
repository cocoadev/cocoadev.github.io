---
layout: page
title: AddTrackingRect
---

Here's the basic steps to add a tracking rect to a custom view.     boundsTrackingTag is an ivar for the tracking rect associated with the bounds of the view (if you would like to receive a mouseEntered/mouseExited event when the cursor enters the visible rect of the view).

    
 - (void)updateBoundsTrackingTag {
     [self removeTrackingRect:boundsTrackingTag];
     NSPoint loc = [self convertPoint:self window] mouseLocationOutsideOfEventStream] fromView:nil];
     BOOL inside = ([self hitTest:loc] == self);
     if (inside) [[self window] makeFirstResponder:self]; // if the view accepts first responder status
     boundsTrackingTag = [self addTrackingRect:[self visibleRect] owner:self userData:nil assumeInside:inside];
 }
 
 - (BOOL)acceptsFirstResponder {return YES;}
 - (BOOL)becomeFirstResponder {return YES;}
 
 - (void)mouseEntered:(NSEvent *)theEvent {
     NSLog(@"<%p>%s:", self, __PRETTY_FUNCTION__);
 }
 
 - (void)mouseExited:(NSEvent *)theEvent {
     NSLog(@"<%p>%s:", self, __PRETTY_FUNCTION__);
 }


Now the important cleanup method. If you forget to remove tracking tags before the view is removed from its window, [[NSApp could send events to this view after it has been deallocated. The source of this type of bug is hard to find. The stack trace will only show that NSApp is trying to send an event, with no reference to the the view the event is trying to send the event to, because the view has been deallocated. A good place to do tracking tag cleanup is in the     viewWillMoveToWindow: method.

    
 - (void)removeTrackingTags {
     [self removeTrackingRect:boundsTrackingTag];
 }
 
 - (void)viewWillMoveToWindow:(NSWindow *)win {
     if (!win && [self window]) [self removeTrackingTags];
     [super viewWillMoveToWindow:win];
 }


--zootbobbalu

----

I just learned the hard way just how incredibly cumbersome addTrackingRect: really is. The code above is NOT enough to properly handle a tracking rect around your view. As a matter of fact, it's nearly impossible to do it at all, without some ugly tricks. The problem is keeping the rectangle updated to the control's position. Overriding setFrame: is not enough, as the superview might change position without the subview's setFrame: ever being called. The trick I finally settled on is overriding drawRect: and changing the tracking rect every time the view is drawn (which should at least keep it in sync with what is shown on screen). (**Update**: Now uses     resetCursorRects instead of     drawRect:)

If you ask me, addTrackingRect: is fundamentally broken, and should not exist. There should really be a much simple method for doing hover effect, especially seeing as how modern GUI use a lot of them. Well, enough ranting, here is the code I used. It's not extensively tested - I'd appreciate any fixes for it:

    
 -(BOOL)acceptsFirstResponder { return YES; }
 
 -(BOOL)becomeFirstResponder { return YES; }
 
 -(void)setTrackingRect
 {
 	NSPoint loc=[self convertPoint:self window] mouseLocationOutsideOfEventStream] fromView:nil];
 	BOOL inside=([self hitTest:loc]==self);
 	if(inside) [[self window] makeFirstResponder:self];
 	trackingtag=[self addTrackingRect:[self visibleRect] owner:self userData:nil assumeInside:inside];
 }
 
 -(void)clearTrackingRect
 {
 	[self removeTrackingRect:trackingtag];
 }
 
 -(void)resetCursorRects
 {
 	[super resetCursorRects];
 	[self clearTrackingRect];
 	[self setTrackingRect];
 }
 
 -(void)viewWillMoveToWindow:(NSWindow *)win
 {
 	if (!win && [self window]) [self clearTrackingRect];
 }
 
 -(void)viewDidMoveToWindow
 {
 	if([self window]) [self setTrackingRect];
 }


--[[WAHa

----
That seems somewhat dangerous to me.. now you're resetting tracking rects way more often than is necessary.

You could try using -[NSView setPostsFrameChangedNotifications:] and NSViewFrameDidChangeNotification on your views superviews.  You'd have to do some work to keep the list of observed views up to date, but it seems doable.


----
Sure, it does it quite often (although not all THAT often if your control is not animated), but I don't see how that would be dangerous. Trying to keep track of superviews seems nearly impossible - you'd have to track how all superviews are added and removed from views, too. I'd say the main problem is that the tracking rectangle system is fundamentally broken and nearly useless. -- WAHa

----

The best place to update tracking rects is in the     resetCursorRects method.

    
 - (void)resetCursorRects {
     [super resetCursorRects];
     /*
         your custom tracking rect code here
     */
 }


This NSView instance method will get called after the position of a view is changed. You are correct in pointing out that my sample code was not complete. The main point I was trying to share was the importance of cleaning up tracking rects from views that are removed from windows.  --zootbobbalu
----

Oh, that's a much nicer kludge.      resetCursorRects was obviously added to deal with the tracking rectangle system being broken, but it sure would have been nice if it was a bit more general. Still, that should work. -- WAHa

----

No prob, I did notice a problem in your tracking tag cleanup code.

    
 -(void)viewWillMoveToWindow:(NSWindow *)win
 {
 	if([self window]) [self clearTrackingRect];
 }
 
 
 You are only checking to see if your view has a window. Sometimes this method gets called multiple times, so you should also check to see if the window being moved to is     nil. 
 
     
 -(void)viewWillMoveToWindow:(NSWindow *)win
 {
 	if (!win && [self window]) [self clearTrackingRect];
 }


--zootbobbalu


----

All right, changed the code to use that. -- WAHa

