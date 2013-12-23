---
layout: page
title: CenteringInsideScrollViews
---

I've gotten it so that my scrollView's contentView gets centered
inside of the scrollview. However, I have a custom cursor set for my
scrollView's documentView. With my centering code in place, the cursor
stays the normal arrow until I get to the centered view (as expected).
However, when I move the mouse below or to the right of my view, the
cursor stays changed instead of reverting to the arrow. Here's the
code that is called whenever the scrollview is resized:
    
- (void)centerCanvas {
   NSRect newBounds = self contentView] bounds];
   [[NSRect newFrame = self contentView] frame];
   if([self frame].size.width > newFrame.size.width || [self
frame].size.height > newFrame.size.height) {
       if((newBounds.size.width - [[self documentView] frame].size.width)/2 > 0)
           newFrame.origin.x = (newBounds.size.width - [[self documentView]
frame].size.width)/2;
       if((newBounds.size.height - [[self documentView] frame].size.height)/2 > 0)
           newFrame.origin.y = (newBounds.size.height - [[self documentView]
frame].size.height)/2;
       
       [[self contentView] setFrame:newFrame];
       [self setNeedsDisplay:YES];
   }
}

I added in the following lines, and the cursor problem was fixed:
    
       newFrame.size.width = [[self documentView] frame].size.width;
       newFrame.size.height = [[self documentView] frame].size.height;

However, with that code in place, the scroll bars do not appear when
they should. So, I know the problem is that the clipview is only
having its origin moved, and its size isn't changing. Does anyone know
how I can fix this?

-- [[MattBall

Try adding     [scrollView reflectScrolledClipView:clipView] at the end of your manipulations.

That didn't make any difference :/ Any ideas? -- MattBall

