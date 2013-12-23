---
layout: page
title: NotRespondingToKeyDown
---

I'm not able to get my sheet window to respond to - (void)keyDown:(NSEvent *)theEvent.  I verified that the first responder is the class where I have put my definition of keyDown.  I have

- (BOOL)acceptsFirstResponder {
    return YES;
}

also in the class.  What I'm really trying to do is make the sheet close when I hit "esc" (escape).  Unfortunately, my computer just beeps at me when I press it.  Other keys also do not show up via my method

- (void)keyDown:(NSEvent *)theEvent {
	NSLog(@"Key down...");
}

I've checked and I *do* get responses to 

- (void)mouseDown:(NSEvent *)theEvent {
	NSLog(@"mouse down...");
}

I'm wondering if it has to do with the NSTextField I have in my sheet.  That is where the cursor is when the sheet is brought in to view.  Thanks!

----

If you have an NSTextField that is selected, then *it* is your first responder, not your window. Your window is in the responder chain, but other objects in the responder chain handle event messages only if the previous objects didn't handle it. The NSTextField most certainly is handling the keyDown:, which is why your window doesn't get the messages.

The correct way to do what you're trying to do is to put a "Cancel" button in the window, and set its key equivalent to be escape. This is more user-friendly (because you can click as well as hitting a key) and takes about two seconds to get working.

----

Of course!  Thank you very much.  I'm deleting my KeyboardEquivalents.m and .h file as I write this... ;)

