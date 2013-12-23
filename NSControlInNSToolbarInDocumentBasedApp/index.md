---
layout: page
title: NSControlInNSToolbarInDocumentBasedApp
---


In my browser, I have an NSToolbar with an AddressField (subclass of NSTextField with autocomplete functionality). Right now, I use what I believe to be a pretty awful solution - when my ToolbarController responds to 

    - (NSToolbarItem *) toolbar: (NSToolbar *)toolbar itemForItemIdentifier: (NSString *) itemIdent willBeInsertedIntoToolbar:(BOOL) willBeInserted

and requests an NSToolbarItem for my AddressField Identifier, I create a new item, and use 

    AddressField *addyField = [[AddressField alloc] initWithFrame:],

 then     [item setView:addyField].

Unfortunately a) I think this leaks memory and b) it means I have to configure my fields entirely by code. It's working relatively ok so far, I haven't been doing any PrematureOptimization so I haven't worried too terribly about whether this particular part of my app is leaking memory. However this appears to be continually creating new copies because the ivars of my subclasses seem to keep resetting. I'm using this same approach on my SearchField, which I got around by using preferences. I needed to use the preferences anyway but I'd prefer to use the ivars rather than continual calls to preferences. Is there any way to build one control in Interface Builder, in a document based app, and have it used in the toolbar without being reinstantiated?

I've tried putting the "template objects" in a holder view, connecting the document's outlet to the AddressField, then an outlet from my ToolbarController to the AddressField. Then, I simply did 

    [item setView:addyField]

 which worked at first, but caused problems after I removed it from the toolbar and went back to customize - I got 

    2006-01-03 22:23:46.434 JustBrowsing[20307] *** -[NSCFString frame]: selector not recognized [self = 0x361d10].

 Does anyone have any tips, or sample code perhaps?

Thanks!
BobInDaShadows

----

Your idea to use an IBOutlet is correct, although I always use the entire holder view as the toobar item's view, you might run into spacing issues otherwise. Here's some sample code to show you how to create the toolbar item. The error you're getting above is probably from trying to access an object that has already been deallocated, so if you're still having trouble, make sure you're retaining your objects where needed, and take a look at them in the debugger.

    
- (NSToolbarItem *)toolbar:(NSToolbar *)toolbar itemForItemIdentifier:(NSString *)itemIdentifier willBeInsertedIntoToolbar:(BOOL)flag;
{
	NSToolbarItem *item = [[NSToolbarItem alloc] initWithItemIdentifier:itemIdentifier];
	
	if ( [itemIdentifier isEqualToString:SearchIdentifier] ) 
	{
		[item setLabel:@"Search"];
		[item setPaletteLabel:@"Search"];
		[item setToolTip:@"Find Things"];
		[item setView:searchItemView];
		[item setMinSize:NSMakeSize( 110.0f, NSHeight( [searchItemView frame] ) )];
		[item setMaxSize:NSMakeSize( 250.0f, NSHeight( [searchItemView frame] ) )];
	}

	return [item autorelease];
}


Excellent! Worked excellently, I don't know what I was doing wrong before but I rewrote my toolbar:itemForIdentifier:willBeInsertedIntoToolbar: method, copied your code in, and it worked! I think my problem may be that I had connected the View outlet to the control itself, instead of the holder view. I appreciate you help, but I'll orphan this now since the question is answered.

