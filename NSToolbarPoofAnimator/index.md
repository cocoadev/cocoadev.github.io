---
layout: page
title: NSToolbarPoofAnimator
---



NSToolbarPoofAnimator is an undocumented AppKit class I found using class-dump. It is known to be present under 10.2, I haven't yet tested under earlier versions.

As you might expect, this class allows you to perform a toolbar-style "poof" animation to indicate the removal of an object. 

Here is the interface to the class. Just create a new NSToolbarPoofAnimator.h file and include it in your project; the implementation is already in AppKit:

    

#import <AppKit/AppKit.h>

@interface NSToolbarPoofAnimator:NSObject

+ (void)runPoofAtPoint:(NSPoint)location;

@end



To begin the animation, just use [NSToolbarPoofAnimator runPoofAtPoint:point]. You might use this in a NSDraggingSource method when something is deleted. It appears the point parameter is ignored, as the poof is always drawn under the current mouse position.

--MichaelRondinelli

----

*Note:* From a helpful friend working on AppKit for the Man: NSToolbarPoofAnimator does indeed respond to the NSPoint argument. Try     [NSClassFromString(@"NSToolbarPoofAnimator") runPoofAtPoint:NSZeroPoint] for an example. Good luck! :)

----

I don't see the standard disclaimer yet. Undocumented classes are usually undocumented for a reason. This functionality might change at any time, even between 10.x.y and 10.x.y+1. If you're fine with that possibility, then rock on.

(JeffHarrell)

----

Panther provides an NSShowAnimationEffect function. You should probably use this instead of the undocumented class.

