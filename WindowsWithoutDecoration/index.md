---
layout: page
title: WindowsWithoutDecoration
---



*This technique is also discussed further and in a more specialized context in General/BorderlessWindow*

You can create windows without titlebars as follows. Subclass the General/NSWindow class in Interface Builder. Call the new class "General/MyWindow" perhaps.
Create a window in General/InterfaceBuilder and use "General/MyWindow" as the custom class. Create the files for General/MyWindow and add them to your project. Include the following code in General/MyWindow.m:

    
@implementation General/MyWindow

- (id)initWithContentRect:(General/NSRect)contentRect 
                       styleMask:(unsigned int)aStyle 
                          backing:(General/NSBackingStoreType)bufferingType 
                              defer:(BOOL)flag {	

    // pass General/NSBorderlessWindowMask so that the window doesn't have a title bar
    self = [super initWithContentRect:contentRect 
                                        styleMask:General/NSBorderlessWindowMask 
					   backing:General/NSBackingStoreBuffered 
					       defer:NO];
	if(self) {
		// This next line pulls the window up to the front on top of other system windows.
		[self setLevel: General/NSStatusWindowLevel];
                // other stuff you may not need
		[self setHasShadow: NO];
		[self setMovableByWindowBackground:YES];
	}
    return self;
}

@end


There are several values you can use in place of General/NSStatusWindowLevel if you don't want the window to always be on top.  See General/NSWindowLevel.

----

You can also     - (void)setIgnoresMouseEvents:(BOOL)YES if you want mouse events to fall through to another view

----
Ummm...One, why can't I just use a normal General/NSWindow and set all this stuff up myself...rather than doing it in a subclass? Two, shouldn't you pass the window-creation options through to super's initWithContentRect::::? --General/JediKnil
    
   self = [super initWithContentRect:contentRect 
                                        styleMask:aStyle | General/NSBorderlessWindowMask // Set General/NSBorderlessWindowMask, keep all other settings.
					   backing:bufferingType 
					       defer:flag];


You can't change a window's style mask, so you need a subclass. But for the other options, there's no need to subclass, it merely makes it more convenient to have it all together.

----

**Borderless, translucent window**

I want to pop up a window like the eject or volume notification in OS X. (Borderless, Translucent). 

I do not even know the name of that UI element so have not got very far googling around.

(The application is to help learn a language (Turkish, French etc not Java, C etc) by presenting the user with flash card like words every few minutes.)

----

You want to use General/BezelServices. You could probably also fake it by using a borderless window set at the appropriate level. The General/RoundTranparentWindow and General/FunkyOverlayWindow Apple General/SampleCode should help.

----

There does not seem to be too much out there on the General/BezelServices so I will start with the samples. 

Links to those samples:
http://developer.apple.com/samplecode/General/FunkyOverlayWindow/General/FunkyOverlayWindow.html
http://developer.apple.com/samplecode/General/RoundTransparentWindow/General/RoundTransparentWindow.html

----

You might want to checkout the source of Disctop 1.0 which we want to release very soon (within a few days) along with an update of Disctop itself.
[http://www.mekentosj.com/disctop/]
Cheers,
Mekentosj 

Note: Although not listed on our site yet, everyone feel free to download the source for Disctop 1.0 through the following direct link:

[http://www.mekentosj.com/disctop/disctop_sourcecode.dmg]

I'd like to see it as a way to thank the many people in the Apple-dev community that helped me started ;-)

----

You should check Growl (General/WhatIsGrowl) http://growl.info One of the authors already published such a notifier, even I think it not included as a part of the package.
-General/BobC

----

The app is called General/VolumeNotifier. its website: http://boredzo.fourx.org/volumenotifier

No, it is not included with Growl.

*--boredzo*
