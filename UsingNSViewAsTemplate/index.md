---
layout: page
title: UsingNSViewAsTemplate
---

I have created an General/NSView that is to serve as a template for several more General/NSViews that will be created. The code looks like this:
 
 
    
- (void)addBookmarksBarItem:(General/NSString *)title withAddress:(General/NSString *)address atLocation:(int)location
{
	
	General/NSData *templateBookmarksBarItem = General/[NSArchiver archivedDataWithRootObject:bookmarksBarItemTemplate];
	id copiedBookmarksBarItemTemplate= General/[NSUnarchiver unarchiveObjectWithData:templateBookmarksBarItem];

	General/NSView* newBookmarksBarItem = [copiedBookmarksBarItemTemplate initWithFrame:General/NSMakeRect(location,0,60,16)];
	[bookmarksBar addSubview:newBookmarksBarItem];
	[bookmarksBarItem setTitle:title];
	[bookmarksBarItem setAlternateTitle:address];
	[bookmarksBarItem setAction:@selector(loadBookmarkItem:)];
}

- (General/IBAction)loadBookmarkItem:(id)sender
{
	General/webView mainFrame] loadRequest:[[[NSURLRequest requestWithURL:[NSURL General/URLWithString:[sender alternateTitle]]]];
}

 
bookmarksBarItemTemplate is the template General/NSView, and bookmarksBarItem is a button that is contained inside bookmarksBarItemTemplate. I have an outlet to both of them set up in IB. 
 
When I run my application, it duplicates the General/NSView the number of times I tell it to, but there are a few problems. Firstly, the action I set to each button doesn't seem to be sticking. Nothing happens when I click any of the duplicated buttons. Secondly, the first duplicated item I create is an EXACT copy of the template (the title, etc didnt get changed, even though I told it to change.) I've spent quite a while trying to fix these two errors to no avail. I'd appreciate any help.

-- General/MattBall

----

    
	General/NSView* newBookmarksBarItem = [copiedBookmarksBarItemTemplate initWithFrame:General/NSMakeRect(location,0,60,16)];
	[bookmarksBar addSubview:newBookmarksBarItem];
	[bookmarksBarItem setTitle:title]; <--
	[bookmarksBarItem setAlternateTitle:address]; <--
	[bookmarksBarItem setAction:@selector(loadBookmarkItem:)]; <--


The lines with arrows to them should send the messages to newBookmarksBarItem and not bookmarksBarItem.

-- General/JesperL
