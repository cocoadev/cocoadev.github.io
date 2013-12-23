---
layout: page
title: MultiPanePreferences
---

Here's a question I know has been asked and asked, but I can't seem to find a good answer to. I want to create a multi-pane preferences window (like in Mail, Safari, or any other Apple app) that uses an NSToolbar to switch between the panes. From what I can tell by looking in the package, each pane is a separate Nib file, which comes together at runtime. I, however, have absolutely no idea where to begin implementing this. I know that you can use OmniAppKit framework to do it, but I just thought it is stupid to include the framework in an app that is only going to use one class, since OmniAppKit is bloated anyway. Anyone have a good idea how to do this? I'm kind of new to Cocoa (only a few months experience) so please bear with me! Thanks! --LoganCollins

*Just for a starter, try looking at Apple's M<nowiki/>ultipleNibTabView example (I<nowiki/>nterfaceBuilder section). Then try making the view tabless, turning off customization on the NSToolbar, and using selectable items to select the tabs. Just an idea. Also, if you want to rely on private APIs (bad idea, but it's worth mentioning), check out NSPreferences. --JediKnil*

I went through the same process you did. I started browsing CocoaDev and couldn't find any help anywhere. I found some code online that promised to make this process easy, but it really just made it more complicated. All I did was go into my MainMenu.nib and make some views. Just drag and drop a "CustomView" from the palette onto your instances window and change the name from "View" to something like "General". Then just put interface items on that and do whatever you want to it. Then in your controller, make an outlet that points to the view you just made (you'll probably want outlets to point to all the interface items you made too. That is why a separate controller could be beneficial for organizational purposes.). Then it is just a matter of setting up an NSToolbar with items that point to some method like "changView:". In changeView, you can just do some checking of the sender NSToolbarItem to switch to the appropriate view. Let me post some sample code.

So in my PreferencesController I have:
    
- (void)awakeFromNib
{
    NSToolbarItem *item;
    items = [[NSMutableDictionary alloc] init];
    
    item = [[NSToolbarItem alloc] initWithItemIdentifier:@"General"];
    [item setPaletteLabel:@"General"];
    [item setLabel:@"General"];
    [item setToolTip:@"General preference options."];
    [item setImage:[[NSImage alloc] initWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"General" ofType:@"tiff"]]];
    [item setTarget:self];
    [item setAction:@selector(switchViews:)];
    [items setObject:item forKey:@"General"];
    [item release];

    //any other items you want to add, do so here.
    //after you are done, just do all the toolbar stuff.
    //myWindow is an outlet pointing to the Preferences Window you made to hold all these custom views.

    toolbar = [[NSToolbar alloc] initWithIdentifier:@"preferencePanes"];
    [toolbar setDelegate:self];  //this is how the toolbar knows what can be selected and such.
    [toolbar setAllowsUserCustomization:NO];  //this is just a pref window, so we don't need to allow customization.
    [toolbar setAutosavesConfiguration:NO];  //we just set everything up manually, so no need for this.
    [myWindow setToolbar:toolbar];  //sets the toolbar to "toolbar"
    [toolbar release];  //setToolbar retains the toolbar we pass, so release the one we used.
    [myWindow center];  //center the window. This is how the pref window should act.
    [self switchViews:nil];  //this is just to make it select General by default.
}


So that sets up all the toolbar stuff and also sets up the NSMutableDictionary that will hold all of our toolbar items. This is needed for the delegate methods. You need to have some kind of global variable to hold this information, so I made it a class variable for PreferencesController. Also, you need a class variable for the toolbar. Lets take a look at the delegate methods for the toolbar (remember, we set the delegate to be PreferencesController in our awakeFromNib.

    
//toolbar delegate methods.

- (NSToolbarItem *)toolbar:(NSToolbar *)toolbar itemForItemIdentifier:(NSString *)itemIdentifier willBeInsertedIntoToolbar:(BOOL)flag
{
    return [items objectForKey:itemIdentifier];
}

- (NSArray *)toolbarAllowedItemIdentifiers:(NSToolbar*)theToolbar
{
    return [self toolbarDefaultItemIdentifiers:theToolbar];
}

- (NSArray *)toolbarDefaultItemIdentifiers:(NSToolbar*)theToolbar
{
    //just return an array with all your items.
    return [NSArray arrayWithObjects:@"General", @"Search", @"Appearance", nil];
}

- (NSArray *)toolbarSelectableItemIdentifiers: (NSToolbar *)toolbar
{
    //make all of them selectable. This puts that little grey outline thing around an item when you select it.
    return [items allKeys];
}

Ok, so that sets up our toolbar. Now we need a way to switch between the views.

    

//called everytime a toolbar item is cilcked. If nil, return the default ("General").
- (void)switchViews:(NSToolbarItem *)item
{
    NSString *sender;
    if(item == nil){  //set the pane to the default.
        sender = @"General";
        [toolbar setSelectedItemIdentifier:sender];
    }else{
        sender = [item label];
    }

    //make a temp pointer.
    NSView *prefsView;

    //set the title to the name of the Preference Item.
    [myWindow setTitle:sender];

    if([sender isEqualToString:@"General"]){
        //assign the temp pointer to the generalView we set up in IB.
        prefsView = generalView;
    }else if([sender isEqualToString:@"Search"]){
        //assign the temp pointer to the searchView we set up in IB.
        prefsView = searchView;
    }else if([sender isEqualToString:@"Appearance"]){
        //assign the temp pointer to the appearanceView we set up in IB.
        prefsView = appearanceView;
    }
    
    //to stop flicker, we make a temp blank view.
    NSView *tempView = [[NSView alloc] initWithFrame:myWindow contentView] frame;
    [myWindow setContentView:tempView];
    [tempView release];
    
    //mojo to get the right frame for the new window.
    NSRect newFrame = [myWindow frame];
    newFrame.size.height = [prefsView frame].size.height + ([myWindow frame].size.height - myWindow contentView] frame].size.height);
    newFrame.size.width = [prefsView frame].size.width;
    newFrame.origin.y += ([[myWindow contentView] frame].size.height - [prefsView frame].size.height);
    
    //set the frame to newFrame and animate it. (change animate:YES to animate:NO if you don't want this)
    [myWindow setShowsResizeIndicator:YES];
    [myWindow setFrame:newFrame display:YES animate:YES];
    //set the main content view to the new view we have picked through the if structure above.
    [myWindow setContentView:prefsView];
}


I'm almost positive I missed something or wasn't clear on a few things. Feel free to post here with questions. Good luck!
--[[ZacWhite

----

That code works perfectly! Many, many thanks for finally solving this problem. --LoganCollins

This was a big help! --NickGerakines

----

Here's a class that will let you add a fully functional preferences window to your application with just a few lines of code (one line for each preference pane). It also adds a nice cross-fading feature (like Apple's DVD Player preferences) when switching between panes.

http://www.mere-mortal-software.com/blog/details.php?d=2007-03-11

There's also a tutorial that will show you how to use this class to add a preferences window to your application, and wire it up to NSUserDefaults using CocoaBindings. It also shows you how to set and use these defaults from your code.

http://www.mere-mortal-software.com/blog/details.php?d=2007-03-14

----

This code will not work out of the box if your sheets are of differing widths (not advisable in most situations but I guess it could come in handy at some point). You can change the frame arithmetic like so in order to keep center-alignment of your sheets: --DanGlegg

    
// Figure out destination frame
NSRect newFrame = [myWindow frame];
newFrame.size.height =	[prefsView frame].size.height + ([myWindow frame].size.height - myWindow contentView] frame].size.height); // Compensates for toolbar
newFrame.size.width =	[prefsView frame].size.width; // this is just the new view's width, unadorned
newFrame.origin.y +=	([[myWindow contentView] frame].size.height - [prefsView frame].size.height); // Origin moves by difference in two views
newFrame.origin.x +=	([[myWindow contentView] frame].size.width - [prefsView frame].size.width)/2; // Origin moves by difference in two views, halved to keep center alignment


----

The various code snippets above to resize the window when the pane changes don't work properly when the UI scale factor is not equal to one. Here's what I use. Run Quartz Debug, bump up the UI Resolution, and give it a go.

    
    float vdiff = ([prefsView frame].size.height - [[window contentView] frame].size.height) * [window userSpaceScaleFactor];
    newFrame.origin.y -= vdiff;
    newFrame.size.height += vdiff;
    float hdiff = ([prefsView frame].size.width - [[window contentView] frame].size.width) * [window userSpaceScaleFactor];
    newFrame.size.width += hdiff;



----

I came across a great article that covers many topics related to the OP's question: http://cocoarocket.com/articles/preferences.html --[[NateVW

