---
layout: page
title: MustSetDelegateToSelfToOverideDeminiaturize
---



Maybe all is fine, I DID find a way to make it work, but having to have my Window subclass set itself as a delegate just doesn't seem right.  My application has two windows.  The "main" window is where graphics is drawn, but it can never be the key window, it is just a place where the application draws lines in response to commands given in a sub window.  I've set up this sub window as a panel, as it is small and supports the main window.  It is always the key window, it is floating and it can be closed (to view the main window better) and reopened.  The main window is there, it cannot be closed.  It goes away only if you quit the application.

My problem was making my main window LOOK like a main window, that is with the title bar and buttons "lit" like a main window.  I've overridden a few of the Window menu items to have Zoom and Minimize act on it and not the Panel.  But sometimes I had to make the main window "main" in my program.

The main window is a subclass of NS window and has the following:

    
- (void) awakeFromNib {
	// so that the Turtle window is highlighted correctly
	[self makeMainWindow];
	// may be wierd but necessary for windowDidDeminiaturize
	[self setDelegate:self];
	// only one window, completely unnessary to have it in the Window menu
	[self setExcludedFromWindowsMenu:YES];
	// needed to take control of Window menu
	[windowMenu setAutoenablesItems:NO];
}

// required for the makeMainWindow call
- (BOOL) canBecomeMainWindow { return YES; }

// keep the statement window the key window
- (BOOL) canBecomeKeyWindow { return NO; }

// for some reason, after deminiaturizing, the Turtle  Window isn't main, this will force it
// need to make self the delegate of self to receive this call
- (void) windowDidDeminiaturize:(NSNotification *) note {
	[self makeMainWindow];
	// reactivate the Window menu items
	[minimizeMenuItem setEnabled:YES];
	[zoomMenuItem setEnabled:YES];
}


As you may be able to see .. when I deminiaturized the window, it would NOT be the main window, I had to make it the main window somewhere.  I tried over riding "deminiaturize", but that routine wasn't even called when I deminiaturized the window.  So finally I had to set self as delegate and put in a windowDidDeminiaturize method.  I even put in a couple of statements to reactive some menu items.  It just doesn't seem right.  There should be a way of getting some code to be called when a window gets deminiaturized without having to go though a delegate call.... or is this whole thing ok and the way you have to do things sometimes.  I'm new to Cocoa and struggling at the moment.

----
When problems like this one crop up, it's often because the responsibilities of each object aren't clear, or are being abused. Your -canBecomeMainWindow and -canBecomeKeyWindow overrides make sense for an NSWindow subclass because they affect how the window itself behaves, and also because there's really no other way to change that behavior. The code in your -awakeFromNib and -windowDidDeminiaturize is different, though: it pertains to the way that the window relates to other windows in the application. That kind of code is usually better placed in a controller object. The whole point of a controller is to know about various objects in the program and manage the relationships between them. Your instinct that a window shouldn't be its own delegate is a good one, because that adds controller responsibilities to an object that's fundamentally a view. It may help to review some of the documentation on the ModelViewController (MVC) paradigm. -CS

----
    -[NSWindow deminiaturize:] is an action that you call to deminiaturize a window, hence the     (id)sender argument.  It's not what's called to actually perform the animation and modify the window loop.  You can actually think of clicking the mini window in the dock as sending     -deminiaturize: itself.

There's no reason that your window itself needs to be its own delegate; you could very well create a new subclass of NSObject that responds to     -windowDidDeminiaturize:, drop an instance in your nib, and wire it up as the window's delegate.

----
That's funny!  Not your responses which I appreciate, but what I did just before posting the original message.  I had the Window subclass with very few methods, so I moved all the window managing methods over to the Window subclass just to make my AppController class smaller.  I wasn't even thinking about the MVC paradigm.  I realize now that the methods really don't belong there, but in AppController or a WindowController subclass.  It did cause a problem.  My floating panel did follow the standard interface of hiding if the user hit the <esc> key.  I had added code to recognize this happening, updating menus and allowing several ways to get it back.  I think I had one Panel delegate method I needed to call and my AppController was the delegate.  When I moved everything over to my Window subclass, it was now the delegate and the program surprised me later when the <esc> key was no longer working right, but would just beep.  It took me a while to figure out what when wrong, only finding out when I started the program from scratch, just adding the window routines and seeing when the Panel lost its standard <esc> response.  I am still not quite sure why it happened, but changing the delegate back to the AppController fixed the problem. (somehow the delegate is in the path of handling the key response?)

I think I subclassed NSWindow initially because I kept getting a crash in my program when my AppController awakeFromNib method tried 

[turtleWindow makeMainWindow]

The only way I could get this to stop crashing was to add 

- (BOOL) canBecomeMainWindow { return YES; }

to the window subclass.  Now it seems to me that the standard window should be able to become a main window without overriding something.  Is there another way to do this?

----
Well, is it a window or a panel? NSPanel will automatically close when you press escape, but cannot by default become the main window, whereas NSWindow won't close on escape but can become main.

----
It is a Panel.  It responded normally on escape until I made a Window subclass the delegate of the Panel.  Then the Panel stopped responding to the escape.  I changed the Delegate back to a controller, and the Panel responded correctly again.  The window I wanted to become main, was a NSWindow, but it would crash until I overrode the canBecomeMainWindow method.

