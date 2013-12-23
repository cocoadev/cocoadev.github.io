---
layout: page
title: NSMenuItemLeak
---

I don't know if this is my fault or not, I truly don't know... Here is the code:

    - (NSMenu *)emoticonMenu
{
	NSMenu * menu = [NSMenu new];
	NSArray * emoticonPacks = [self emoticonPacksInMainBundle];
	
	int i;
	for (i=0; i<[emoticonPacks count]; i++)
	{
		// Add the title item that says which emoticonpack this comes from.
		[menu addItemWithTitle:emoticonPacks objectAtIndex:i] objectForKey:@"Name"]
						action:nil 
				 keyEquivalent:@""];
		
		// Create a bundle with the path, this solves so many problems just 
		// using an [[NSBundle, LOTS of headaches solved.
		NSBundle * bundle = [NSBundle bundleWithPath:emoticonPacks objectAtIndex:i] objectForKey:@"[[FullPath"]];
		
		// Inside the info.plist file is our array of icons, this is nessesary.
		NSArray * icons = bundle infoDictionary] objectForKey:@"Icons"];
		
		int j;
		for (j=0; j<[icons count]; j++)
		{
			// Inside every entry in that array is a dictionary with all the attributes.
			[[NSDictionary * dict = [icons objectAtIndex:j];
			
			NSImage * image = [[[NSImage alloc] initWithContentsOfFile:
				[bundle pathForResource:[dict objectForKey:@"Icon"] ofType:nil]] autorelease];
			NSString * name = [dict objectForKey:@"Name"];
			NSString * textRep = [dict objectForKey:@"TextEmoticon"];
			
			// There is a leak in this menuitem code
			NSMenuItem * item = [[NSMenuItem alloc] init];
			[item setImage:image];
			[item setTitle:name];
			// Using a tooltip to pass which emoticon we want inserted saves us 
			// from SOOO much trouble, it has the added bonus of showing the user.
			[item setToolTip:textRep];
			[item setAction:@selector(emoticonMenuAction:)];
			[menu addItem:[item autorelease]];
		}
	}
	return [menu autorelease];
}

I'd give much thanks to anyone who can help me solve this problem in cocoa that has been haunting me in many apps.

You'll be leaking     image, because it's created but never released. Could that be what you're thinking of? (Also, I really don't understand what kind of strangeness you're doing with the tooltip, so if it involves that, I'm just confused.)

    image *is autoreleased.  Scroll to the side.*

Yeah Its not the Image or the ToolTip, but this isn't the first time I've had this problem with NSMenuItems, it has happened every time I have tried to make them programatically, none of my solutions have worked so i decided to ask the more experienced people here :).

----

What's your process for deciding there's a leak here?  

----

Well, as soon as I call this code, the leak shows up, I comment out the NSMenuItem code, and the leak disappears, so it isn't the NSMenu, I know that much.

----

I mean, how are you measuring the leak.  No matter how you're measuring it, it sounds like you're doing it in the context of your app, which doesn't necessarily help since something else may be retaining one of your objects.  Try setting up your app so that nothing is done with the returned menu (so that everything should be deallocated along with the autorelease pool), and so that the method is called a very large number of times.  That should help you decide if *this* is really the code that leaks.  (My guess: no.)

----

Well I don't have the time to do that at the moment, however supposing you are right and the leak isn't here and it is where this is called;

    
- (void)windowWillLoad
{
	...
	emoticonMenu = self emoticonMenu] retain];
	...
}
- (void)dealloc {
	...
	[emoticonMenu release];
	[super dealloc];
}


I have to retain it or it will crash, and I can't call that every time because that is inefficient. However as you can see above, I am releasing it at dealloc. The only time this code is called is in a contextual menu. *sigh* 

    
- (void)emoticonMenu:([[NSToolbarItem *)item
{
	[NSMenu popUpContextMenu:emoticonMenu 
				   withEvent:[NSApp currentEvent] 
					 forView:self window] contentView;
}


----

Alright I did what you said, and just calling [self emoticonMenu];x10 does nothing, no leak, nadda, etc... All right, it isn't this code itself, and it only happens when we load it for the first time, this leads me to believe -popUpContextMenu:withEvent:forView: is the culprit, would you be inclined to agree?

----

Yes, could very well be.  I'd make sure that your containing object's dealloc method is actually called, though.  What are you seeing in your tool?  What is leaking?  Does it leak more if you use the popup menu more than once?  Does it not leak if you never call up the popup menu?

----

It did leak when i called the popup more then once when i told the popup to call the generated menu every time it popped up, now it leaks only once, but yes it only happens when you call the popup not before.

The strange thing is it doesn't always leak, for instance now I'm getting 0 leaks for 0 total leaked bytes, but ten minutes ago it didn't (this is a separate launch) and I didn't touch any of it.

----

