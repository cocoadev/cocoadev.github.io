---
layout: page
title: NSButtonFunctionKeyEquivalent
---



You can set an NSButton<nowiki/>s key equivalent to a function key using:

    
unichar arrowKey = NSLeftArrowFunctionKey;
[previousButton setKeyEquivalent:[NSString stringWithCharacters:&arrowKey length:1]];


When you do this for the arrow function keys (with no modifier specified) the action of the button is invoked twice, once on keyUp and once on keyDown - this does not happen if you do specify a modifier.

Does anyone know why or have a fix (I think it's bug raising time...)??

----

Yeah, it's a Cocoa bug.  You can patch it in a future-safe way by overriding -[NSButton performKeyEquivalent:] and returning NO if the event is of type NSKeyUp.  There will be no attempt to perform key equivalents for key up events in the future (and even for now, there usually isn't).

