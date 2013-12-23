---
layout: page
title: InvalidatingShadowDoesNotReflectRecentlyHiddenAndShownViews
---

I am working on application that creates a full screen window, has a NSMovieView taking up the full window, and then I have added a child window that contains some text fields, etc.  The idea is to have a form that is viewable/editable above the movie playing in the background.  I figured out how to make the form editable by overrding - (BOOL)canBecomeKeyWindow to return yes for the child window.  I am also using a shadow on the child window (and a clear background color obviously) so that the labels and items stand out a little more on top of the video.

Everything so far is good.  However, as part of the form, I created two custom subviews, in the sense of having two steps.  When you finish the first step, you click continue, and I hide the first subview, and then show the second subview.  I'm not sure if there's a better way to do this (I didn't want to use the NSTabView because I didn't want the tab controller at the top, and couldn't find a way to hide the tab controller without hiding the NSTabViewItem children as well).  Anyway, so my solution has been to programatically show/hide these two views when the "Next" button is clicked:

    
    // hide name, show contact information
    [fullScreenNameView setHidden:YES];
    [fullScreenContactView setHidden:NO];
    
    // select address
    [fullScreenAddressField selectText:nil];
    
    // update
    // [fullScreenWindow messageWindow] contentView] setNeedsDisplayInRect:[[fullScreenWindow messageWindow] frame;
    // [fullScreenWindow messageWindow] contentView] setNeedsDisplayInRect:[[[fullScreenWindow messageWindow] contentView] frame;
    // [fullScreenWindow messageWindow] contentView] setNeedsDisplay:YES];
    // [fullScreenNameView setNeedsDisplay:YES];
    // [fullScreenContactView setNeedsDisplay:YES];
    
    // invalidate shadow
    [[fullScreenWindow messageWindow] invalidateShadow];


I also have another method that does the reverse (hides the first view, shows the second one).  The problem is that the shadow seems to be re-calculated based on the view before the two subviews have been hidden/shown.  So what happens is I get a ghosting type effect, where I can still see the shadow from the first view's items even though that view is hidden.  And the shadow for the second view is not there.  Then, when I flip them back and the shadow is invalidated again, it's backwards too.  So I see the shadow for the second view's items but only the first view is visible.  I can keep flipping them the views back and forth but it keeps showing the shadow from the previously visible view.

I though maybe the view needed to be manually redrawn before I invalidated the shadow, so I tried to make some different calls to try to redraw the views, with no luck.  The lines commented above are the lines I tried that did not do what I was hoping they would do...

Anyone have any ideas?  I don't know enough about how the shadow is generated (ie. when and where the shape for the shadow is determined) to really go any further myself.

fullScreenWindow is the full screen window, and it has a messageWindow getter that simply returns a reference to the child window.

Thanks in advance,
[[PercyHanna

----

I finally found that one that seemed to make more sense and should work, but it only works 50% of the time, very randomly.  The only connection I've been able to find is that the time between cliking from the first page to second page and back seems to affect whether or not the shadow is drawn properly...

    
    fullScreenWindow messageWindow] display];
    [fullScreenWindow display];


Again, this only works half the time.  If I click the buttons very fast it rarely ever works, if I wait a second or so it has a better chance of getting it right, but still not 100%.

Thanks again for any possible help.  -[[PercyHanna

----

The following is of the style I've used before, insert in the main view and set changed to YES when you need the shadow recalculated. I agree its not very nice... - RbrtPntn
    
- (void)drawRect:(NSRect)rect {
    // - normal drawing stuff...
    if(changed) {
        [[fullScreenWindow messageWindow] invalidateShadow];
        [[fullScreenWindow messageWindow] update];
        changed = NO;
    }
}

