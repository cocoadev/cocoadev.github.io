---
layout: page
title: DockSize
---

How do I know how much I can programatically resize my window before running into the dock (if it's shown).

----

    [[[NSScreen screens] objectAtIndex:0] visibleFrame].size will return the NSSize of the screen, minus the dock & menubar.

----

Ah cool, I was looking for just this, but never got around to posting.

----

The following makes a window full screen (except for the dock and menu). It should be noted that     [aWindow setFrame:aWindow screen] visibleFrame] display:YES] does the same thing as all the code below. However, this code demonstrates how to determine the dock's orientation (bottom, left, or right), the height of the dock (if on the bottom), and the width of the dock (if on the left or right), should you need to do so. aWindow is any window on the screen. Depending on what you want to do, you can use [[[NSScreen mainScreen] instead of [aWindow screen].

    

	float visibleScreenWidth = aWindow screen] visibleFrame].size.width;
	float visibleScreenHeight = [[aWindow screen] visibleFrame].size.height; // doesn't include dock and menu bar
	BOOL dockOnBottom = false;
	
	if(visibleScreenWidth == [[aWindow screen] frame].size.width)
	{
		dockOnBottom = true;
	}
	else
	{
		if(0 == [[aWindow screen] visibleFrame].origin.x)
		{
			// dock is on right
		}
		else
		{
			// dock is on left
		}
	}	
	
	[[NSRect rect;
	rect.origin.x = 0.0;
	rect.origin.y = 0.0;
	rect.size.width = visibleScreenWidth;
	rect.size.height = visibleScreenHeight;
	
	if(dockOnBottom)
	{
		float heightOfDock = aWindow screen] visibleFrame].origin.y; 
		rect.origin.y = heightOfDock;
	}
	else
	{
		//float widthOfDock = [[aWindow screen] frame].origin.x - [[aWindow screen] visibleFrame].origin.x;
		rect.origin.x = [[aWindow screen] visibleFrame].origin.x; // add width of dock if on left (will add zero if on right)
	}	
	
	[aWindow setFrame:rect display:YES];



----

You can get 'live updates' on the usable screen area by registering for the following notifications:

    

    [[[[NSNotificationCenter defaultCenter] addObserver:self 
                                             selector:@selector(screenChanged:) 
                                                 name:NSApplicationDidChangeScreenParametersNotification 
                                               object:nil];
    
    [[NSDistributedNotificationCenter defaultCenter] addObserver:self 
                                                        selector:@selector(screenChanged:) 
                                                            name:@"com.apple.dock.prefchanged" 
                                                          object:nil];



----
Is there a way to get the Dock's size if it's auto-hidden? You know, visibleFrame only subtracts the Dock's height / width if it's always visible, otherwise it only subtracts 3 or 4 pixels for the tracking rect...
Sure, you could read out the tilesize from com.apple.dock.plist, but if the Dock has a lot of items, the Dock can't be resized to its full extent (128 for height), there's always 30 pixels to each edge of the screen; so it wouldn't be accurate... Any ideas?

