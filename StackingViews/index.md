---
layout: page
title: StackingViews
---

Generally in Cocoa it's not a good idea to have views overlapping - the frontmost view should be a subview of the containing view instead. This avoids hacky RAD-like tricks which invalidate views at odd times to try to get them to draw and update. Unfortunately, Interface Builder does not have the ability to arbitrary specify a view as a subview of another. For example, I have a NSTextField with an NSButton near the right hand side. In IB, the text field can be placed on the window, and the button placed on top of that. This appears to work well when testing the interface, but in the application the button often disappears if the text field is updated. This seems reasonable since the button and field are actually on the same level as far as Cocoa is concerned. To get the button to appear even if the text field is updated, I made the button a subview of the field. This is the code I used:

In the containing window controller's     awakeFromNib:
    
  [fButton retain];
  [fButton removeFromSuperviewWithoutNeedingDisplay];
  [fTextField addSubview:fButton];
  [fButton setFrameOrigin:NSMakePoint([fButton frame].origin.x, -1)];
  [fButton release];


I had to specify the button's frame origin explicity because by placing it on the window in IB, its origin is relative to the window's content view. When it becomes a subview of the text field, its origin becomes relative to the text field.

