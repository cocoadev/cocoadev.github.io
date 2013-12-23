---
layout: page
title: GameKeyboardHandlingAlmost
---

I provide an alternate solution I believe to be much easier at GameKeyBoardHandling.
--AlainODea
----

I've seen a lot of discussions on how to do proper game-like handling of the keyboard in cocoa apps. Most of the answers involve writing your own app-loop or using some scary Carbon level stuff, involving turning off keyboard repeat and other things which I'd rather not delve into.

What I've figured out is a way, in cocoa, to make ~90% "Good Enough" game keyboard handling, where you can have multiple keypresses simultaneously ( as in, Left + Up  at same time ) and handling of shift, ctrl, option and command keys independently.

The reason I say it's only ~90% complete, is because handling of the Command key isn't quite right. If you hold command and then press and release an arrow key, the keyUp operation is never called. Baffling. As far as I can tell every other combination works ( as in Shift, Ctrl, Opt ) Perhaps somebody can add some code below to fix the problem.

Here's the header for GameView, which in your game might better be derived from NSOpenGLView or some more appropriate for games.

*There's a small problem with using this code which arises when the user depresses a key with shift held down, releases the shift key and then releases the held-down key. The key recorded as being pressed and the key recorded as being released differ, leading to your game believing the key has not been released.*

*This can be fixed by storing the key returned by [self translateKeyCode: key] in an array indexed by [theEvent keyCode] in keyDown, and then using that array indexed [theEvent keyCode] to provide the key that is released in keyUp, this ensures that the key released is always the same as the key depressed.*

That explains behavioral oddities I had, but didn't consider worth fixing. Care to provide a fix? My best guess ATM is to have translateKeyCode detect if the key was a non-arrow key and simply to convert it to lowercase.

*Okay, I fixed up the code using **key_pressed_for_keycode** to store the translated keypress between press and release, that solves the problem with the shift key being pressed or released while a key is held down. I used these very routines in Oolite* ( http://oolite.aegidian.org/ )



*AH - 1/5/2005 - Tiger (Mac OS X 10.4) seems to have broken this by intercepting some key events before they are sent to the view.*

**I've long since dropped this for an overidden NSApplication::sendEvent; seems to work fine on tiger** --ShamylZakariya

----

    

/*
    Since the Cocoa constants for arrow keys are left shifted by 16, 
    they can't fit in the bool array. So they're being defined at the end
    going inwards.
*/
enum GameViewKeys {
	gvArrowKeyUp = 255,
	gvArrowKeyDown = 254,
	gvArrowKeyLeft = 253,
	gvArrowKeyRight = 252
};

@interface GameView : NSView
{
	BOOL keys[256];
        // FIX : store key pressed for a particular keycode
        int key_pressed_for_keycode[256];
	BOOL opt, ctrl, command, shift;
}

- (BOOL) isDown: (int) key;
- (BOOL) isOptDown;
- (BOOL) isCtrlDown;
- (BOOL) isCommandDown;
- (BOOL) isShiftDown;
- (int) numKeys;



And the definition:

    

@interface GameView(Internal)

- (int) translateKeyCode: (int) input;

@end

@implementation GameView

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {}
	return self;
}

- (void)drawRect:(NSRect)rect
{
}

- (BOOL) acceptsFirstResponder
{
	return YES;
}

- (void) keyUp:(NSEvent *)theEvent
{
	int key = theEvent charactersIgnoringModifiers] characterAtIndex:0];
	// FIX :
        // key = [self translateKeyCode: key];

        // FIX : retrieve actual key pressed for this keycode
        int keycode = [theEvent keyCode] & 255;
        key = key_pressed_for_keycode[keycode];

	if ( key >= 0 && key < [self numKeys] )
	{
		keys[key] = NO;
	}
	else
	{
		[[NSLog( @"translated key: %d out of range\n", key );
	}
}

- (void) keyDown:(NSEvent *)theEvent
{
	int key = theEvent charactersIgnoringModifiers] characterAtIndex:0];
	key = [self translateKeyCode: key];	

        // FIX : store actual key pressed for this keycode
        int keycode = [theEvent keyCode] & 255;
        key_pressed_for_keycode[keycode] = key;

	if ( key >= 0 && key < [self numKeys] )
	{
		keys[key] = YES;
	}
	else
	{
		[[NSLog( @"translated key: %d out of range\n", key );
	}
}

/*
    Capture shift, ctrl, opt and command press & release
*/
- (void)flagsChanged:(NSEvent *)theEvent
{
	int flags = [theEvent modifierFlags];

	opt = (flags & NSAlternateKeyMask) ? YES : NO;
	ctrl = (flags & NSControlKeyMask) ? YES : NO;
	command = (flags & NSCommandKeyMask) ? YES : NO;
	shift = ( flags & NSShiftKeyMask ) ? YES : NO;
}

/////////////////////////////////////////////////////////////

/*
    Turn the Cocoa ArrowKeys into our arrow key constants.
*/
- (int) translateKeyCode: (int) input
{
	int key = input;
	switch ( input )
	{
		case NSUpArrowFunctionKey:
			key = gvArrowKeyUp;
			break;
	
		case NSDownArrowFunctionKey:
			key = gvArrowKeyDown;
			break;
	
		case NSLeftArrowFunctionKey:
			key = gvArrowKeyLeft;
			break;
	
		case NSRightArrowFunctionKey:
			key = gvArrowKeyRight;
			break;
			
		default: break;
	}	

	return key;
}


/////////////////////////////////////////////////////////////

- (BOOL) isDown: (int) key
{
	if ( key < 0 ) return NO;
	if ( key >= [self numKeys] ) return NO;
	return keys[key];
}

- (BOOL) isOptDown
{
	return opt;
}

- (BOOL) isCtrlDown
{
	return ctrl;
}

- (BOOL) isCommandDown
{
	return command;
}

- (BOOL) isShiftDown
{
	return shift;
}

- (int) numKeys
{
	return 256;
}

@end



One thing to remember, somewhere you must call something like:      [mainWindow makeFirstResponder: gameView]; 

If you forget, you'll not receive the key events in the first place.

----

Usage is simple -- it assumes you have a game loop and as such is designed for polling. Each time your loop runs, have a method, for example      -(void) pollKeyboard;  which looks for the state of certain keys...

I used it in a manner like this:

    

- (void) pollKeyboard
{	
	static int velocityIncr = 4;
	static int sidestepIncr = 4;
	static int pitchIncr = 4;
	static int heightIncr = 4;
	static int turnIncr = 4;
	
	int scale = [self isShiftDown] ? 120 : 60;
	int turnScale = [self isShiftDown] ? 4 : 1;	

	if ( [self isDown: glvArrowKeyUp] )
	{
		if ( [self isOptDown] )
		{
			velocity += velocityIncr;
		}
		else if ( [self isCtrlDown] )
		{
			height += heightIncr;
		}
		else
		{
			pitch -= pitchIncr;
		}
	}

	if ( [self isDown: glvArrowKeyDown] )
	{
		if ( [self isOptDown] )
		{
			velocity -= velocityIncr;
		}
		else if ( [self isCtrlDown] )
		{
			height -= heightIncr;
		}
		else
		{
			pitch += pitchIncr;
		}
	}
	
	if ( [self isDown: glvArrowKeyLeft] )
	{
		if ( [self isOptDown] )
		{
			sidestep += sidestepIncr;
		}
		else
		{
			turn += turnIncr;
		}
	}

	if ( [self isDown: glvArrowKeyRight] )
	{
		if ( [self isOptDown] )
		{
			sidestep -= sidestepIncr;
		}
		else
		{
			turn -= turnIncr;
		}
	}


	/*
	    My API was mouse based; likely I'll write a more direct camera 
	    movement interface that's more appropriate for keyboarding.
	*/
	if ( sidestep || velocity )
	{
		GLVisualization::instance()->moveCamera( RIGHT_MOUSE, 
				(int) sidestep * scale,  
				(int) velocity * scale );
	}
	
	if ( turn || pitch )
	{
		GLVisualization::instance()->moveCamera( LEFT_MOUSE, 
				(int) turn * turnScale,  
				(int) pitch * turnScale );
	}
	
	if ( height )
	{
		GLVisualization::instance()->moveCamera( MIDDLE_MOUSE, 0,  
				(int) height * scale );
	}
	
	
	//now, let our scalars fade
	velocity = (velocity * 7) / 8;
	sidestep = (sidestep * 7) / 8;
	height = (height * 7 ) / 8;
	pitch = ( pitch * 5 ) / 8;
	turn = ( turn * 5 ) / 8;
		
	
}



This works well enough for what I'm doing, which isn't a game, but being a environment/ai simulation does involve chasing robots around so good keyboarding is useful.

Of course, if you're writing a serious game, you'd probably be using pure Carbon or SDL.

--ShamylZakariya

Why would you? Omni uses Cocoa for their game ports, and they're definitely serious.

**Well, chances are quite good that only the window which houses the game's graphics, an NSOpenGLView, some sort of event-handling and possibly a preferences window are done in Cocoa. Most likely the original game was C++ or even C code, and converting it all to Objective-C would be too time-consuming (not to mention pointless and taxing on the frame rate).**

----

I've run into the same command key oddness when trying to do something similar. Try overiding -sendEvent and passing NSFlagsChanged events to yourself...
    
- (void)sendEvent:(NSEvent *)anEvent
{
    switch([anEvent type]) {
        case NSKeyDown:
             // [self processKeyDownEvent:anEvent];  ...?
        case NSKeyUp:
             // [self processKeyUpEvent:anEvent];  ...?
        case NSFlagsChanged:
            [self processFlagsChangedEvent:anEvent];
    }

    [super sendEvent:anEvent];
}


I think that way you get a chance to see if it's up or down before it gets stripped out.

----

There appears to be a bug in AppKit, where key up events that are pressed while holding the command key don't get routed to the NSWindow. They do, however, get routed through the NSApplication. Therefore it's fairly trivial to handle that ourselves.

Add this to your NSApplication subclass:

    

- (void)sendEvent:(NSEvent *)anEvent{
	//This works around an AppKit bug, where key up events while holding
	//down the command key don't get sent to the key window.
	if([anEvent type] == NSKeyUp && ([anEvent modifierFlags] & NSCommandKeyMask)){
		self keyWindow] sendEvent:anEvent];
	}else{
		[super sendEvent:anEvent];
	}
}



This will ensure that you get your command key-up'd events. --[[SteveStreza

----
THANK YOU for this bug fix!  I was banging my head at this for *hours* until I stumbled across this.  Why hasn't this bug been fixed yet?
----

----

From the developer documentation, as of July 2011:

"NSApplication sends a Control-key event to the key window via performKeyEquivalent:  before sending it as an NSKeyDown event through the responder chain."

You could add something like this to your NSView subclass for it to receive the equivalent of a Cmd-Key press:

    

- (BOOL)performKeyEquivalent:(NSEvent *)theEvent
{
	//----------------------------------------------------------------------
	// Variables:
	
	UInt16			keyCode					= [theEvent keyCode];
	
	unichar			uniCode					= [[theEvent charactersIgnoringModifiers] characterAtIndex:0];
		
	//----------------------------------------------------------------------
	// Redirect key up event:
		
	switch ( keyCode )
	{
		case KEY_CODE_D:  // KEY_CODE_D is 2.
			
			doSomethingHere( keyCode, uniCode );

			return YES;       // We have handled the key press, so return YES.
			
			break;
	}
	 
	return NO;  // We have not handled the key press, send if through the responder chain.
}



----

