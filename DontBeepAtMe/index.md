---
layout: page
title: DontBeepAtMe
---

My application occasionally requires a sequence of backspace keypresses to be sent to the front process. This works fine, but if the FirstResponder doesn't accept the character, it will beep. Now, this only occurs rarely, so it's not a huge deal to me, but I'd like to be able to suppress those beeps. I could do this by using an ApplicationEnhancer-like system, but I think that's a little involved for my simple project. Is there a better way? I'd like to avoid turning the volume off, as well.

-- RyanGovostes

----

You could put something higher in the responder chain that responds to the iirc **-delete:** selector, but does nothing. Then the key is handled and there won't be a beep.

----

I'm not familiar with the     delete: selector and couldn't find it in the docs, were you refering to     keyDown:? In that case you would pass it on to     super if the delete key is not being pressed. Here's some code.

*Sorry, I meant -deleteBackward:*

    
- (void)keyDown:(NSEvent *)event
{
	if (event characters] characterAtIndex:0] != [[NSDeleteCharacter) {
		// Only pass it on to super if it's not the delete key.
		// This will cause a beep for all other key downs
		[super keyDown:event];
	}
}


You could put this in an NSWindow subclass or, if you want it to take effect globally for your application, use an NSApplication subclass.

CORRECTION: NSApplication does not receive the keyDown event after NSWindow, so the latter suggestion will not work. You will have to implement it for each window in your app: http://developer.apple.com/documentation/Cocoa/Conceptual/BasicEventHandling/Concepts/KeyEventPath.html

-- RyanBates

----

Just did a test and     deleteBackward: doesn't even get called in an NSWindow subclass. Perhaps it only works for NSView subclasses? Is it even directly related to key down events? -- RyanBates

----

True, these would work if my application is frontmost, but not if it's someone else's. Hmm... Seems tricky. -- RyanGovostes

*I'm a little confused. Why would you want to intercept the backspace key when another application is active? Wether it beeps or not is up to the frontmost application in those circumstances. Tricky is probably an understatement when it comes to this. ;)*

----

NSApplication does not define a     keyDown: method, but it does define     sendEvent: which can be used to catch all events in an application.

----
I just added this line to my subclassed window code and it took care of it. - Charlie

    
-(void)noResponderFor:(SEL)keyDown { /* overriding this removes the System Beeps */ }

