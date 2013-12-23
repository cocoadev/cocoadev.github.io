---
layout: page
title: ValidatingMenuItemForDrawerOperation
---

Does anybody know of a better way in a document-based app?

The only way I could figure out to do it for this simple example was (using General/NSNotificationCenter):

    
- ( void ) drawerStateChanged: ( General/NSNotification * ) notif
{
	General/NSDrawerState st = [ controlsDrawer state ];
	id drawerMenuItem;
	
	if ( st == General/NSDrawerOpenState || st == General/NSDrawerOpeningState )
	{
		drawerMenuItem = [ [ [ [ [ General/NSApplication sharedApplication ] mainMenu ] 
			itemWithTitle: @"Window" ] submenu ] itemWithTitle: @"Open Drawer" ];
		[ drawerMenuItem setTitle: @"Close Controls Drawer" ];
	}
	else
	{
		drawerMenuItem = [ [ [ [ [ General/NSApplication sharedApplication ] mainMenu ] 
			itemWithTitle: @"Window" ] submenu ] itemWithTitle: @"Close Drawer" ];
		[ drawerMenuItem setTitle: @"Open Controls Drawer" ];
	}
}


----

Use something like

    

-(BOOL)validateMenuItem:(General/NSMenuItem *)anItem
{
    if ( [ anItem action ] == @selector( toggleControlsDrawer: ) ) 
    {
        if ([controlsDrawer state] == General/NSDrawerOpenState ||
              [controlsDrawer state] == General/NSDrawerOpeningState)
        {
            [anItem setTitle:@"Close Controls Drawer"];
        else
        {
            [anItem setTitle:@"Open Controls Drawer"];
        }
    }

    return YES; //or do other stuff here to enable/disable items
}


