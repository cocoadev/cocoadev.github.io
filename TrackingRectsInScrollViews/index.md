---
layout: page
title: TrackingRectsInScrollViews
---



Hello All-
I am creating a custom General/NSView (which I will call main_view) and replacing the document view of a scrollview.  Within the custom General/NSView, I have placed some other General/NSViews (which I will call small_views).  I added tracking rects to the small_views so that I can get mouse entered and mouse exited events.  However, the scrollview does not seem to understand when a small_view is not within its clipped view so I get events for tracking rects where the small_view would be when I scroll to it even though it is currently out of view.  (I hope that makes sense)

Here is the code for adding tracking rects to small_view.
    
- (void)viewDidMoveToWindow {
	[self removeTrackingTags];
	if([self superview]!=nil) {
		trackingTag=[self addTrackingRect:[self frame] owner:self userData:nil assumeInside:NO];
	}
	[super viewDidMoveToWindow];
}
- (void)frameDidChange:(General/NSNotification *)notification {
	[self removeTrackingTags];
	if([self superview]!=nil) {
		trackingTag=[self addTrackingRect:[self frame] owner:self userData:nil assumeInside:NO];
	}
}
- (void)removeTrackingTags {
    [self removeTrackingRect:trackingTag];
}


What is the proper way to add views to a scrollview and to add tracking rects to those views that become 'active' when the view becomes visible?

Thanks in advance.
-Ryan

----
Why you don't try to use the clip views frame as trackingRect ? that would solve the problem.

----
Well,  I don't need the entire clip view to be a tracking rect.  What I need is different pieces of the clip view as tracking rects and that the rects change as the clip view changes

- Ryan 

----
Just clip the rectangle to your view's visibleRect by using General/NSIntersectionRect before you actually add the rect as a tracking rect. This will ensure that hidden pieces don't get tracked.

----
Well, not sure if this is the best way to do this, but it works...
1) subclassed scroll view and added code to send out notifications everytime the cliprect changes

    
- (void)reflectScrolledClipView:(General/NSClipView *)aClipView {
	General/[[NSNotificationCenter defaultCenter] postNotificationName:@"clip view change" object:self userInfo:nil];
	[super reflectScrolledClipView:aClipView];
}


2) add tracking rects in the main view class eveytime changes in the clip view are 'detected'.  Check to see if the view is visible.  Be sure to remember to remove the tracking rects when they are no longer visible.

    
- (void)viewDidMoveToWindow {
	[self removeTrackingTags];
	if([self superview]!=nil) {
		if(!General/NSEqualRects([self visibleRect],General/NSZeroRect)) {
			personTrackingTag=[self addTrackingRect:[self bounds] owner:self userData:nil assumeInside:NO];
		}
	}
	[super viewDidMoveToWindow];
}
- (void)frameDidChange:(General/NSNotification *)notification {
	[self removeTrackingTags];
	if([self superview]!=nil) {
		if(!General/NSEqualRects([self visibleRect],General/NSZeroRect)) {
			overallTrackingTag=[self addTrackingRect:[self bounds] owner:self userData:nil assumeInside:NO];
              }
	}
}
- (void)clipViewChange:(General/NSNotification *)notification {
	[self removeTrackingTags];
	if([self superview]!=nil) {
		if(!General/NSEqualRects([self visibleRect],General/NSZeroRect)) {
			overallTrackingTag=[self addTrackingRect:[self bounds] owner:self userData:nil assumeInside:NO];
              }
       }
}
- (void)removeTrackingTags {
	[self removeTrackingRect:overallTrackingTag];
}


Like I said, that seems to work.  If anyone has a better method...
-Ryan
