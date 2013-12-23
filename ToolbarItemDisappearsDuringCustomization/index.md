---
layout: page
title: ToolbarItemDisappearsDuringCustomization
---

When clicking customize for my toolbar, all items which are represented by custom views suddenly disappear, or rather are moved to inside the customization sheet.  Moving them around happens as would be expected, except that the view can only be in one place at a time.  

http://idisk.mac.com/omnius/Sites/General/DisappearingToolbarItem.tiff

I have confirmed that I'm using that standard code from Apple:

    
    // Use a custom view, a rounded text field,
    // attached to searchFieldOutlet in General/InterfaceBuilder as
    // the custom view 
    [toolbarItem setView:searchFieldOutlet];
    [toolbarItem setMinSize:General/NSMakeSize(100,General/NSHeight([searchFieldOutlet frame]))];
    [toolbarItem setMaxSize:General/NSMakeSize(400,General/NSHeight([searchFieldOutlet frame]))];


And I backed down from my own view to just a vanilla General/NSSlider (to ensure that General/NSCoding isn't the issue) and the problem persists.  I can't find any mention of this problem... what's going on?

----
Ok, so I finally got this working right.  Basically, General/NSToolbar uses two techniques which, for me, seem counter-intuitive though I can see why they are a good angle of attack for the efficiency Apple clearly wanted from this class.

1) the same General/NSToolbarItem is used repeatedly until it is explicitely removed from the toolbar.

2) new instances of the General/NSToolbarItem are created hen you call the customization palette, up to two.  But they are not used.

Therefore, the solution to the first (and only thusfar stated problem) is that whenever itemForItemIdentifier:... is called, you should create a new view which will be displayed in the customization sheet.  It is most likely that you are not going to take a big hit for this, unless you've got a R-O-U-S up there in your toolbar.  Unfortunately, this does preclude using IB at all for custom views.  I notice that though there is an IB search field in Apple's sample code (General/SimpleToolbar), if you look into the code a new searchfield is in fact created every time the toolbar asks for the item.  

The magic, then, is in using the delegate methods toolbarWillAddItem: and toolbarDidRemoveItem: to configure your new object.  I ran into a crasher which killed my app all day, and it was due to the fact that my General/NSToolbarItem's view was bound to another value.  To make matters worse I was setting up this binding within toolbar:itemForItemIdentifier:willBeInsertedIntoToolbar:.    Do not do any configuration beyond the visual in this method.  This binding must be programatically configured AND dismantled to prevent a crash when the message is sent to released memory space.

    
- (void)toolbarWillAddItem:(General/NSNotification *)notification
{
	General/NSToolbarItem *item = General/notification userInfo] valueForKey:@"item"];
	
	if ( [[item itemIdentifier] isEqualToString:@"myItemIdentifier"] ) {
		// Bind, say, the title of the toolbar item to some other element
		[item bind:@"label" toObject:[item view] withKeyPath:@"keyPath" options:nil];
		
		// Set the item to receive messages whenever some change occurs
		[otherObject addObserver:[item view] forKeyPath:@"keyPath" options:nil context:@"context"];
	}
}

- (void)toolbarDidRemoveItem:([[NSNotification *)notification
{
	General/NSToolbarItem *item = General/notification userInfo] valueForKey:@"item"];
	
	if ( [[item itemIdentifier] isEqualToString:@"myItemIdentifier"] ) {
		// Must deregister to prevent crashes from messages send to released addresses
		[item unbind:@"label"];
		[otherObject removeObserver:[item view] forKeyPath:@"keyPath"];
	}
}


Hope this helps some other poor soul!

----
I think this could help too.  I find it maddening that there is no way to ask a toolbar for the item named @"name", so here's that method (though it comes with a caveat)

    
- ([[NSToolbarItem *)itemForItemIdentifier:(General/NSString *)itemIdentifier
{
	General/NSArray *items = [self items]; //Do NOT reference this function from within the first setup run of toolbar:itemForItemIdentifier:willBeInsertedIntoToolbar: because it calls -items recursively; since you can't use _currentItems (it's private), the only solution is to make sure this is called only after the toolbar has been set up (with a state var such as BOOL inFirstRun), or do not use it from within said delegate method
	
	if ( General/items valueForKey:@"itemIdentifier"] containsObject:itemIdentifier] )
		return [items objectAtIndex:[[items valueForKey:@"itemIdentifier"] indexOfObject:itemIdentifier;

	return nil;
}

