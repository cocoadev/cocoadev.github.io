---
layout: page
title: NSPopUpMenuHelp
---

I don't know how to use the NSPopUpMenu. Can anyone help me?

----

Add this to the view where you want the context menu to pop up.

    
-(id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        menu=[[NSMenu alloc] init];
        id item=[[[NSMenuItem alloc] initWithTitle:@"doSomething" action:@selector(doSomething) keyEquivalent:@""] autorelease];
        [menu addItem:item];
    }
    return self;
}

-(void)dealloc {
    [menu autorelease];
    [super dealloc];
}

-(void)doSomething {
    NSLog(@"doingSomething");
}

-(void)mouseDown:(NSEvent *)theEvent {
    NSLog(@"mouseDown:");
    [NSMenu popUpContextMenu:menu withEvent:theEvent forView:self];
}


--zootbobbalu

----
Thanx

----
Is there a good way to control the exact positioning of the popup menu? For example, if I want a consistant position for a popup menu that appears when a user clicks on a button?

----

If you want a button to control the menu, then just use an NSPopUpButton. You can add these in IB!! --zootbobbalu

Alternatively, you can make a new cell subclass which does the popping of menus as appropriate, make a button in IB, and have the button use your cell class-- it honestly is preferable sometimes. -- RobRix

But what if you want a dropdown from a toolbar? Adding a button with no boarder and an image with a some transparency seems to screw up the toolbar's background drawing. What would be great is if you could force the toolbar item's submenu to appear no matter what format the toolbar is set to (ie - not just in text-only view). Does anybody know what I'm getting at? I think Keynote does this...

since this does work in text-only view on toolbars, maybe there's a non-public call that can do the trick?

