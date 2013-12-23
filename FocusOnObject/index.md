---
layout: page
title: FocusOnObject
---

I was just wondering how to change the focus (focus ring and cursor) to an object in the nib. Can anyone enlighten me?

----

You mean programatically? If so, thusly:
    
[window makeFirstResponder:myObject]


If you want the window to open with a particular object as first responder, connect the window's initialFirstResponder outlet to that object in the nib.

A NSTabViewItem instance also has an initialFirstResponder outlet to specify which object should have focus when this item is chosen in the NSTabView.

----
----

Once my program receives a specific event, I'd like a window to appear and set focus on an NSTextField. Here's the code I'm trying now:

    [self orderFrontRegardless];
[self makeKeyWindow];
[self makeFirstResponder:textField];

However, <code>textField</code> is never given focus. Am I doing something wrong here?

-- RyanGovostes

----

have you tried     [self makeKeyAndOrderFront:nil]?

----

This does not seem to work either. If the window is key, does it even receive keyDown events? If so, I could just ditch the NSTextField, but when I write this in my Window subclass, nothing happens when I hit a key:

    - (void)keyDown:(NSEvent *)theEvent {
    NSLog(@"KeyDown event!\n");
}

-- RyanGovostes

----

Try removing the text field and overriding     -(BOOL)acceptsFirstResponder in your window class, returning     YES, then the     keyDown event should fire. Works for me.

----

Yeah, the     keyDown event fires, but if the cursor is in a different window, it still doesn't receive the event.

see (perhaps relevant) discussion at SimulateTyping

