---
layout: page
title: NSMenuUnavailableWithoutNib
---



I'm trying to port an app to OS X, and I'm currently setting up the menu system. I am not using a nib, so I'm doing it manually through NSApplication:setMainMenu, NSMenu, and NSMenuItem.

The problem I have here is that no matter what I do, the menus do not show up at all. Some quick research online turns up some unconfirmed anecdotes that menus must be created before NSApplication:run, so here's what I'm doing before then:

    

	if( ![NSApp mainMenu] )
	{
		NSMenu	*main_menu = [[NSMenu alloc] initWithTitle:@""];
		
		[NSApp setMainMenu: main_menu];
		
		[main_menu release];
	}

	NSMenu		*app_menu;
	NSMenuItem	*app_item;
	NSString	*app_name = @"MyApp";
	
	NSString	*item_title;
	
	app_menu = [[NSMenu alloc] initWithTitle: @""];
	
	// Quit
	item_title = [@"Quit " stringByAppendingString: app_name];
	[app_menu addItemWithTitle: item_title 
						action: @selector(terminate:) 
				 keyEquivalent: @"q"];
	
	// done with all items
	app_item = [[NSMenuItem alloc] initWithTitle: @"" 
										  action: nil 
								   keyEquivalent: @""];
	[app_item setSubmenu: app_menu];
	[[NSApp mainMenu] insertItem: app_item
						 atIndex: 0];
	
	[item_title release];
	[app_menu release];
	[app_item release];



That is just trying to set up a very basic app menu with a Quit item. The app compiles fine, and none of the calls return any errors in debug, so all appears to be well, but upon starting app all there is is the default "AppName" menu, with no items in it.

Any ideas on what to do?

----

If you need to do something with     NSApp before     -[NSApplication run], then you have to do it in     main(), and can't use     NSApplicationMain().  Otherwise     NSApp == nil and your messages will succeed but not do anything.

----

I just ran a debug through the code. NSApp != nil at the time I'm making all of these calls. I've made sure to call NSApplication sharedApplication before entering the function, since that's supposed to guarantee a good NSApp pointer. I gather not using NIBs at all is unorthodox when it comes to Cocoa development, but in my situation it can't be helped.

Just to be safe I've checked Console for any errors that may have been silently printed, and there's nothing there either. As far as I can tell the whole thing should just work.

----
What *is* your situation? Are you sure it can't be helped? People frequently believe that they can't use nibs when that's not actually the case, which is why I ask.

Anyway, this topic is a perennial question on the cocoa-dev mailing list, so search the archives on cocoabuilder.com and I'm sure you'll discover how to make it work.

----

Since this is a port, there's a heavy push to using existing UI config files from other platforms to generate the menu, and this isn't done until well after NSApplication:run. One idea is to use a nib file that creates a do-nothing menu, if only to get *some* sort of menu in before NSApplication:run. That's the solution I will be trying next, though it still confuses me why I cannot simply use the API to create menus.

----
Yes, I would highly encourage you to use a bare-bones MainMenu.nib instead of trying to do everything by hand. It will eliminate a lot of code and reduce the probability of failure in the future. As a bonus, platform-specific stuff like the application menu can stay in the nib instead of having to dirty up your config files with Mac-only items.

----

For the sake of posterity, here's the solution I've come to:

- NSMenu class contains an internal var named "name", which must be set to "NSMainMenu" for the main menu, and "NSAppleMenu" for the app menu. When using the standard Cocoa API to set up your menus in-code, your NSMenu items need to have those names for the menus to generate, not to mention all of this needs to be done before the app is finished initialization.

I have another problem now, though. I have set up my Application menu, and I'm adding my File, Edit, etc etc items in-code. They are added properly, but all "top level" menus have text that overlap (i.e. the "File" and "Edit" text on the top menu bar sit on top of each other). Anybody ever run into this?

----

Have you thought about dropping into Carbon for this?  Just put a bare-bones (Quit item only) menu in your main nib, and then use the Carbon Menu Manager ( http://developer.apple.com/documentation/Carbon/Reference/Menu_Manager/index.html ) to modify your menu.  But note that using Carbon will exclude your app from 64-bit land.

----
The 10.5 AppKit release notes contain this paragraph:

*In Tiger, the initial label appearing in the menu bar was taken from the submenu's title. But due to a bug, changes to this menu's title did not affect the menu bar; instead, the parent item's title was used instead. So for example, the title of the File submenu determines the initial label, but to change the the label, you must modify the title of the File item itself.*

It sounds likely that this is the cause of your bug.

