---
layout: page
title: AlteringTheAppearanceOfNSToolbar
---

Is it possible to alter the Appearance of the NSToolBar? What i would ideally want is to be able to set the appearance of it to be a transparent grey.

Is this possible? 

--TomHancocks

----

Looks a little iffy. NSToolbar does not inherit from a view class, so there are no obvious methods to override. I *think* that NSToolbar is transparent, so by changing the appearance of the background window, the toolbar will look different. I have not confirmed this.

----

It's probably easiest to just make your own, using something like UKDistributedView.

----

Well I'm only asking as I'm in the process of making TuneX 2, and I have already ported it to Cocoa from REALbasic. I need the semi-transparent look like in the first one, as thats what everyone loved about it, and i'm wantting items to be rearrangeable and customizable. Thanks anyways --TomHancocks

----

You mean this?

http://macupdate.com/images/screens/uploaded/17512_scr.png

You don't need a NSToolbar for that, NSButton<nowiki/>s in a custom view, inside a borderless NSWindow would work better. For rearranging and customizing, see UKDistributedView above. NSToolbar isn't meant to be used like that, and making it work like that would probably be harder and not work nearly as well as rolling your own.

*Seriously, listen to the above posters. I 'third' that motion. Trust us, banging away at the NSToolbar Goliath is only sensible when you're David. *The* David, smartass. ;-)*

----

OK, thanks for your help

----

More alteration of NSToolbar: some code to get to the toolbar view and to the contextual menu, assuming [self window] returns the window you are interested in. The menu can be changed in the awakeForNib: method, assuming the toolbar is not already in use by other windows

I just spent an hour getting to this, and I thought it could be useful for others. Or maybe there is a simpler way??

    
	NSView *borderView = self window] valueForKey:@"_borderView"];
	[[NSEnumerator *e =borderView subviews] objectEnumerator];
	[[NSView *toolbarView;
	while ( ( toolbarView = [e nextObject] ) && ( ![toolbarView isMemberOfClass:NSClassFromString(@"NSToolbarView")] ) )
			;
	NSMenu *toolbarContextualMenu = [toolbarView menu];


----

To get instance of NSToolbarView (NSView subclass) you can just use this undocumented method:
    
 NSToolbarView *view = [self window] toolbar] _toolbarView];
 if ( ![view superview] ) return; //this toolbarview exists, but it's not attached to window right now
 [view lockFocus];
 /* ... */

 You may also use **_setToolbarView:** and **_setToolbarViewWindow:** methods of [[NSToolbar to make your own custom view appear in your toolbar.

-- DenisGryzlov

