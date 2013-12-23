---
layout: page
title: AnimatedNSSplitView
---

Animating



== Using     -animator Proxy ==

Here's a method written for a subclass using vertical dividers. Obviously this can be edited to be used in a controller object -- and two more lines of code can add support for horizontal dividers.

 - (void)setSplitterPosition:(CGFloat)newPosition
 {
     NSView * view0 = [self.subviews objectAtIndex:0];
     NSView * view1 = [self.subviews objectAtIndex:1];
     
     NSRect view0TargetFrame = NSMakeRect(view0.frame.origin.x, view0.frame.origin.y, newPosition, view0.frame.size.height);
     NSRect view1TargetFrame = NSMakeRect(newPosition + self.dividerThickness, view1.frame.origin.y, NSMaxX(view1.frame) - newPosition - self.dividerThickness, view1.frame.size.height);
     
     [NSAnimationContext beginGrouping];
     [[NSAnimationContext currentContext] setDuration:0.2];
     view0 animator] setFrame:view0TargetFrame];
     [[view1 animator] setFrame:view1TargetFrame];
     [[[NSAnimationContext endGrouping];
 }


I should also mention that for some reason -splitView:resizeSubviewsWithOldSize: gets repeated called during the animation, even though the splitView isn't being resized. This may or may not be what you want, so you may want to check NSEqualSizes( sender.frame.size, oldSize) at the beginning of the function.
-- Jeffrey



== Using NSViewAnimation ==

 - (IBAction)animateSplitView:(id)sender
 {
     static float s_delta = 130;
 
     NSRect frame = [targetView frame]; //assumes you have an outlet defined to the view you want to resize
  
     frame.origin.y += s_delta;
     frame.size.height -= s_delta;
 
     NSDictionary *windowResize windowResize = [NSDictionary dictionaryWithObjectsAndKeys:
         targetView, NSViewAnimationTargetKey, 
         [NSValue valueWithRect: frame],
         NSViewAnimationEndFrameKey,
         nil];
 
     NSViewAnimation * animation = [[NSViewAnimation alloc] initWithViewAnimations:[NSArray arrayWithObject: windowResize]];
     [animation setAnimationBlockingMode:NSAnimationBlocking];
     [animation startAnimation];
     [animation release];
 
     s_delta *= -1;
     s_delta += 1;
 
     [splitView display]; //assumes outlet defined for splitview, needed so that split view redraws correctly after animation.
 }




== Another With NSViewAnimation ==

This code also works; put it in a subclass of NSSplitView.

 // Unhides both subviews and changes the splitter position, possibly
 // with animation. This method's behavior is undefined if there are not
 // exactly two subviews. Note that the delegate must call
 // -setNeedsDisplay:YES whenever -isSplitterAnimating returns YES.
 
 - (void)setSplitterPosition:(float)newSplitterPosition animate:(BOOL)animate
 {
    if (self subviews] count] < 2)
        return;
 
    [[NSView *subview0 = self subviews] objectAtIndex:0];
    [[NSView *subview1 = self subviews] objectAtIndex:1];
 
    [[NSRect subview0EndFrame = [subview0 frame];
    NSRect subview1EndFrame = [subview1 frame];
 
    if ([self isVertical]) {
        subview0EndFrame.size.width = newSplitterPosition;
 
        subview1EndFrame.origin.x = newSplitterPosition + [self dividerThickness];
        subview1EndFrame.size.width = [self frame].size.width - subview0EndFrame.size.width - [self dividerThickness];
    } else {
        subview0EndFrame.size.height = newSplitterPosition;
 
        subview1EndFrame.origin.y = newSplitterPosition + [self dividerThickness];
        subview1EndFrame.size.height = [self frame].size.height - subview0EndFrame.size.height - [self dividerThickness];
    }
 
    // Be sure the subview isn't hidden from a previous animation.
    [subview0 setHidden:NO];
    [subview1 setHidden:NO];
 
    // Update subviewEndFrame.origin so that the frame is positioned
    if (animate) {
        NSDictionary *subview0Animation = [NSDictionary dictionaryWithObjectsAndKeys:
            subview0, NSViewAnimationTargetKey,
            [NSValue valueWithRect:subview0EndFrame], NSViewAnimationEndFrameKey, nil];
        NSDictionary *subview1Animation = [NSDictionary dictionaryWithObjectsAndKeys:
            subview1, NSViewAnimationTargetKey,
            [NSValue valueWithRect:subview1EndFrame], NSViewAnimationEndFrameKey, nil];
 
        NSViewAnimation *animation = [[NSViewAnimation alloc] initWithViewAnimations:[NSArray arrayWithObjects:subview0Animation, subview1Animation, nil]];
        [animation setAnimationBlockingMode:NSAnimationBlocking];
        [animation setDuration:0.3];
        // Use default animation curve, NSAnimationEaseInOut.
 
        isSplitterAnimating = YES;
        [animation startAnimation];
        isSplitterAnimating = NO;
 
        [animation release];
    } else {
        [subview0 setFrame:subview0EndFrame];
        [subview1 setFrame:subview1EndFrame];
    }
    [self adjustSubviews];
 }
 
 // Only works with two subviews.
 - (float)splitterPosition
 {
     if ([self isVertical])
         return [self frame].size.width - [self subviews] objectAtIndex:0] frame].size.width;
     else
         return [self frame].size.height - [[[self subviews] objectAtIndex:0] frame].size.height;
 }
 
 - (BOOL)isSplitterAnimating {
    return isSplitterAnimating;
 }
 
 ///////////// [[NSSplitView delegate
 
 - (void)splitView:(NSSplitView *)sender resizeSubviewsWithOldSize:(NSSize)oldSize {
    // Don't interfere with animation.
    if ([sender isSplitterAnimating]) {
        // I got infinite recursion when I used plain old -display.
        [sender setNeedsDisplay:YES];
        return;
    }
    // Do whatever else you want to do in this delegate.
 }

