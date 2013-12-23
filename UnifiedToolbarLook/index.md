---
layout: page
title: UnifiedToolbarLook
---



Hi!
I'm trying to set the new toolbar look programmatically (I know I can do it in IB), I've found this in carbon


    
#import <Carbon/Carbon.h>

//#define kWindowUnifiedTitleAndToolbarAttribute (1 << 7) defined in Carbon

void *ref = [mainWindow windowRef];
General/ChangeWindowAttributes(ref, kWindowUnifiedTitleAndToolbarAttribute, 0);

but it returns an error when I compile:
says kWindowUnifiedTitleAndToolbarAttribute is never defined before.

http://developer.apple.com/qa/qa2005/qa1423.html

Tnx

*Have you added the Carbon framework to your project?*

Yes

----

Instead of dropping to Carbon, why not just override the General/NSWindow style mask in a custom subclass and pass in General/NSUnifiedTitleAndToolbarWindowMask, the way it's done on General/BorderlessWindow?

----

Doesnt work: ERROR: Can't have a toolbar in a window with <General/NSNextStepFrame: 0x326250> as it's borderview

General/PostYourCode

----

sry here is my code:

    
General/MyWindow.m

- (id) initWithContentRect:(General/NSRect)contentRect styleMask:(unsigned int)aStyle backing:(General/NSBackingStoreType)bufferingType defer:(BOOL)flag
{
self = [super initWithContentRect:contentRect styleMask:General/NSUnifiedTitleAndToolbarWindowMask backing:General/NSBackingStoreBuffered defer:flag];
    return self;
}


 ----

I would start by preserving the style mask and backing store handed to you, like so:

    
- (id) initWithContentRect:(General/NSRect)contentRect styleMask:(unsigned int)aStyle backing:(General/NSBackingStoreType)bufferingType defer:(BOOL)flag
{
self = [super initWithContentRect:contentRect styleMask:aStyle | General/NSUnifiedTitleAndToolbarWindowMask backing:bufferingType defer:flag];
    return self;
}


*tnx*

----
I know this is probably evil, but I'd like to implement this look in an app that has no toolbar.  I just think it looks way cleaner.  Any idea on how to do this?

william
----
What do you mean? Where would the gradient go? The unified toolbar look is, besidees the toolbar itself, exactly the same as the normal non-brushed-metal windows. --General/JediKnil

----
Perhaps he means the iTunes look. In that case, he could give this a read: http://www.rogueamoeba.com/utm/posts/Article/polished-metal-2005-09-10-10-00.html

----
Yes that's closer to it, although not the dark.  I had seen that article but I was hoping it wasn't the only way (other than waiting for Apple to standardize their interface...that will be the day).  If anyone's used Iridium they'll know what I'm talking about.  Thanks,

william

----
Answered my own question.  For those that care, its a bit simpler than the above tutorial if all you want is one smooth gradient.  First I created the gradient in photoshop, sampling the colors from Safari, then, I just put the following method in awakeFromNib:
    
	General/NSColor *color = General/[NSColor colorWithPatternImage:General/[NSImage imageNamed:@"gradient"]];
	[theWindow setBackgroundColor:color];

Corner's are rounded, looks great.  Note that you'll have to do a bit more for a resizable window (see the comments of the above tutorial), and things don't act quite like unified-title apps when they are in the background (unified-title apps have pinstripes when they are in the background).  Hope that helps,
william
