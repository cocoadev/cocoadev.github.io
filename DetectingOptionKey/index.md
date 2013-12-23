---
layout: page
title: DetectingOptionKey
---



I'm trying to get a Cocoa application to implement an iTunes like behaviour: when you hold down the Option key, the
"Add playlist" button turns into an "Add smart playlist".  keyDown doesn't seem to get the option press, so I'm assuming it got swallowed
somewhere along the way?  Does anyone know how to grab this?

----

Try overriding the flagsChanged: method instead of keyDown. Perhaps something like this.

    
- (void)flagsChanged:(NSEvent *)theEvent
{
	if ([theEvent modifierFlags] & NSAlternateKeyMask) {
		// Option key pressed
	}
}


----

A related question - in order to hook into the responder chain at this level, I ended up subclassing NSWindow
and adding the flagsChanged method there.  I had thought the window's delegate was also somehow part
of the responder chain (based on Apple docs) but it doesn't get called?  Is there a coherent reason why?

----

There are two types of messages which get sent down the responder chain: **event messages** and **action messages**. Basically, event messages do not get sent to the window's delegate, however, action messages do. Action messages are usually sent through Interface Builder's "First Responder." See apple's docs for more information: [http://developer.apple.com/documentation/Cocoa/Conceptual/BasicEventHandling/Concepts/AboutRespChain.html] -- RyanBates

