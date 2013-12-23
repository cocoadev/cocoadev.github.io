---
layout: page
title: OverridingSendEventMethod
---

I have a custom NSView subclass and want to control keyboard input from that custom view.  keyDown: won't work until the view becomes the firstResponder -- looking at books I have I don't see one sure-fire way to do this.  Once FirstResponder is set, keyDown: should work, yes?

Tnx.


blakespot

----

Make sure you put this method in the NSView subclass:

    
- (BOOL)acceptsFirstResponder
{
	return YES;
}


This method returns NO by default (in the NSResponder class) which is why you need to override it and return YES in your subclass.

If you need to retrieve key down events without the view being first responder, you will need to subclass NSWindow and override the keyDown: event there. If you need to grab it before it gets all the way down to the NSWindow, you can subclass NSApplication and override the sendEvent: method which is the first point your application knows of the event. -- RyanBates

----

Could you provide some tips/docs/example-code for overriding sendEvent: on NSApplication? I'm abstractly curious, since I've implemented this on NSView before and couldn't trap the command key. Which happened to be the correct behavior there -- since it was a normal app and I didn't want to lose keyboard shortcuts -- nonetheless I'm curious should I ever write a twitch-game. --ShamylZakariya

----

Here's an example of the sendEvent: method you can put in an NSApplication subclass.

    
- (void)sendEvent:(NSEvent *)event
{
	// Only report when modifier keys change so we don't get a ton of logs
	if ([event type] == NSFlagsChanged) {
		// Check if the command key is held down
		if ([event modifierFlags] & NSCommandKeyMask) {
			NSLog(@"Command key is pressed");
		} else {
			NSLog(@"Command key is not pressed");
		}
	}
	
	// Forward the events to superclass for normal processing
	[super sendEvent:event];
}


You will also need to tell the target to use your NSApplication subclass as the principle class. You can change this in the "Cocoa Specific" section in the target settings.

If you are creating a game, you may also consider making your own run loop and handling all the events manually.

-- RyanBates

----

My question is in the interest of getting me further along on the creation of this simple game I've spoken of in other threads.  Is using keyDown: check the best way to track a prolonged keydown that might appear in a game?  Would this be only appropriate for a single keypress such as would be carried out when a user is in a text editor?  Thanks.


blakespot

----

I haven't had any problems with this method in the past. If it's for a prolonged event, just set a BOOL variable to YES on the keyDown: and then NO on the corrosponding keyUp: event for that specific key. -- RyanBates

Also, see: GameKeyboardHandlingAlmost, GameInputHandling and HandlingMultipleKeyDown

----

Ok it's working for me - as for the BOOL var tho.  I can set in the keyDown, but where would you recommend I clear it (when no key pressed) so that I can discern between it being set or cleared?  In the func called by my timer?  Tnx.

blakespot

*On keyUp:*

