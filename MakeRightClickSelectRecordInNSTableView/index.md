---
layout: page
title: MakeRightClickSelectRecordInNSTableView
---

Hi,

I'm sure this must have been answered at least once, but I have tried and failed to find any related docs.

How can I make a right click (context-click) select a row on an NSTableView as well as bring up the menu. Default behaviour is NOT to select the row under the mouse.

Thanks for any help offered,

-Peter

----

I'm pretty sure you have to subclass NSTableView to do this.  I'm guessing it'd look something like this:
    
@implementation MyTableView
- (void)rightMouseDown:(NSEvent*)inEvent
{
	[self selectRow:[self rowAtPoint:[self convertPoint:[inEvent locationInWindow] fromView:nil]] byExtendingSelection:NO];
	[super rightMouseDown:inEvent];
}
@end

Hope this helps.  -- Bo

----

I have subclassed the NSTableView as suggested, but for some reason I override      rightMouseDown:(NSEvent *)theEvent  and it never gets called. I override mouseDown:... and that overrides as would be expected. It seems that rightMouseDown is not being called at all for some reason.

Any suggestions?

----

Are you using a real right mouse button, or are you control-clicking? As far as I know, OS X treats them as two separate beasts, though they perform the same action by default.

I'm doing the same thing in an application I'm working on, and I decided to try putting the selection code into - (NSMenu *)menuForEvent:(NSEvent *)event instead. This seems to work perfectly (and makes a bit more sense to me since I'm also enabling/disabling items based on the selected rows).

-- Wevah

----

See RightClickSelectInTableView

