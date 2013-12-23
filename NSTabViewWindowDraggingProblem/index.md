---
layout: page
title: NSTabViewWindowDraggingProblem
---

My app window is marked as 'has textured' in IB.

I have an NSTabView in this window, and when I select the second tab (of 3) when I click and drag on any control or view in the window - the window will move and the intended action will take place. In this tab I have a NSSplitView one side being a NSImageView (inside a  NSScrollView) and the other side a custom view (inside an NSScrollView). In the case that I click and resize the NSSplitView, as I drag the mouse the NSSplitView is resized AND the window moves (both happen at the same time).

This only happens after I select 2nd tab, and it keeps happening until i either resize this window or select tab 1 or tab 3.

I tried playing around with making different controls the first responder after the tab change, but that doesn't seem to have any effect. I'm not sure what to try next... Any ideas?

----

Are the tab view or its items created or modified in code? Are any of them subclasses of NSTabView or NSTabViewItem? Also note that a textured window always moves with a drag on the window itself (as in, no views above it claiming the mouse events). You can turn this off with -setMovableByWindowBackground: ... I believe custom views must do something with mouse events (so they're 'swallowed' and not allowed to drop through to the view beneath it - your window). I don't know if this applies to your situation (because you haven't posted any code), but it's a place to start. *See also SetMovableByWindowBackground*

----

Yes, I do modify some of this tab's contents in code (posted below). I sort of found the problem I think while thinning down the code to post. When I comment out the "animation code" below, the problem is fixed, but I don't understand why it's fixed. Each of my tab view's tabs have a method similar to reconfigureForSortTab: and they all perform an animated window resize and control fade in animation, but this is the only one with the problem.

    
- (void)awakeFromNib;
{
    [controlPanelsInBox retain];
    [controlPanelsInBox removeFromSuperviewWithoutNeedingDisplay];
    mainWindow contentView] addSubview: [controlPanelsInBox autorelease] positioned: [[NSWindowBelow relativeTo: [sortTabMainSplitView superview]];

    [self reconfigureForSortTab: mainWindowTablessTabView ];
}

-(void)reconfigureForSortTab:(id)sender;
{
    // [snip -- some view resizing and new window size calculations, etc... ]

    [mainWindowToolbarTabSegmentedControl setEnabled: NO ];
    sortTabMainSplitView superview] setHidden: YES ];
    [controlPanelsInBox setHidden: YES ];

// begin animation code
    [[NSMutableDictionary *theDict = [NSMutableDictionary dictionaryWithCapacity:3];
    [theDict setObject:[sortTabMainSplitView superview] forKey:NSViewAnimationTargetKey];
    [theDict setObject:NSViewAnimationFadeInEffect forKey:NSViewAnimationEffectKey];
    [theDict setObject:[NSValue valueWithRect: boxSize] forKey:NSViewAnimationEndFrameKey];
    
    NSMutableDictionary *theOtherDict = [NSMutableDictionary dictionaryWithCapacity:3];
    [theOtherDict setObject: controlPanelsInBox forKey:NSViewAnimationTargetKey];
    [theOtherDict setObject:NSViewAnimationFadeInEffect forKey:NSViewAnimationEffectKey];
    [theOtherDict setObject:[NSValue valueWithRect: tabSize] forKey:NSViewAnimationEndFrameKey];
    
    theAnim = [[NSViewAnimation alloc] initWithViewAnimations:[NSArray arrayWithObjects:theDict, theOtherDict, nil]];
    [theAnim startAnimation];
// end animation code

    [mainWindowToolbarTabSegmentedControl setEnabled: YES ];
}


*Is this tab presently empty? Also, you say other tabs have 'similar' methods ... how similar? What's different?*

