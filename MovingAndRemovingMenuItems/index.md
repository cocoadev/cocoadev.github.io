---
layout: page
title: MovingAndRemovingMenuItems
---



I have a problem that is completely baffling me; I'm putting in a feature in my app that allows a menu item from the menu bar to be moved to the application's menu as a submenu (to save menu bar real estate if the user needs it) and back again if desired. I've got this working (in the code below) pretty well: if the menu in question is initially a top level (in the menu bar) menu, when I select to move it as a submenu, it disappears from the menu bar and appears as a submenu as expected. When I move it back, it disappears from the submenu, appears in menu bar. But this is where it stop working correctly; if I try to move it once again off the menu bar and into a submenu, it does get added as a submenu in the app's menu item, but also remains in the menu bar, and is still usable even tough the log message shows it is no longer in the main menu structure in memory, and I can no longer get rid of it from the menu bar (though trying to move it back and forth still removes it when a submenu as expected).

I'm rather confused as to why it doesn't work; however I did notice that the problem occurs once my menu is initially moved back to the menu bar, which leads me to believe that it might be a problem in either when I add it as a submenu, or how I add it to the menu bar, but I can't see anything wrong.

Any thoughts?  --Seb

    

-(IBAction)moveMenu:(id)sender
{
	// move the menu in/out of main menu bar

	// hidden = 0 = in menu bar; global var
	// myOwnMenu is the menuitem being moved; alloc'ed but not autoreleased so that it doesn't get lost when removing during move
	// myFromNib is the menu loaded from the nib attached as submenu to myOwnMenu
	
	int index,menuPlace=0;
	NSMenu *appMenu=[[[NSApp mainMenu] itemAtIndex:0] submenu];  // the app's menu item in the menu bar
	NSMenuItem *myOwnMenu;

	NSLog(@"menu before:%@",[NSApp mainMenu]);

	if(hidden) // hidden in the app's menu, move it to menu bar
	{
		index=[appMenu indexOfItemWithSubmenu:menuFromNIB];
		myOwnMenu =[appMenu itemAtIndex:index];
		[appMenu removeItem: myOwnMenu];
		
		// just add it to the end in the menu bar
		[[NSApp mainMenu] addItem:myOwnMenu];
	}
	else // in menu bar, move the app's menu
	{
		index=[[NSApp mainMenu] indexOfItemWithSubmenu:menuFromNIB];
		myOwnMenu =[[NSApp mainMenu] itemAtIndex:index];
		[[NSApp mainMenu] removeItem: myOwnMenu];
		  // update: see note below on what to add here


		// hide it in app's main menu, after preferences just before next separator
		NSMenuItem *tempItem;
		while(tempItem=[appMenu itemAtIndex:menuPlace])
		{
			if(tempItem keyEquivalent] isEqualToString:@","] && ([tempItem keyEquivalentModifierMask] & [[NSCommandKeyMask))
				break;
			menuPlace++;
		}
		while(!appMenu itemAtIndex:++menuPlace] isSeparatorItem]){}
		[appMenu insertItem: myOwnMenu atIndex:menuPlace];
	}

	[[NSLog(@"menu after:%@",[NSApp mainMenu]);

	hidden=!hidden;
}


Sorry, but my only thought is 'NSMenu does it again'. I can't tell you how weird NSMenu has acted for me, sometimes the 'fixes' I found made no sense (reversing two method calls) and other times I have had to change to a different system. Also, let th wiki know what dev-system you are using (10.3 or Tiger?). --JeremyJurksztowicz

----
I'm coding on Tiger, but it also happens on 10.3.9

And it looks like I'm not the only one that's run into this without a solution ( http://www.cocoabuilder.com/archive/message/cocoa/2000/7/28/34692 ) - I'd definitively call this a bug in the OS.  --Seb

----

OK, found it! Seems one must post a notification that the main menu changed when removing the item; so the above code is updated to (relevant section):
    
[[NSApp mainMenu] removeItem: myOwnMenu];
[[NSNotificationCenter defaultCenter] postNotificationName:@"NSMenuDidRemoveItemNotification" object:[NSApp mainMenu] userInfo:[NSDictionary dictionaryWithObject:[NSNumber numberWithInt:index] forKey:@"NSMenuItemIndex"]];


Note: while the docs state that using     removeItemAtIndex (which I had tried) automatically posts that notification, it still seems necessary to do so, at least for the app's main menu. But it still doesn't explain why it initially worked only once and not on subsequent attempts.  --Seb

