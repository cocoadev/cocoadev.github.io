---
layout: page
title: SpeedyGonzalez
---



**Speedy Gonzalez**
----*"OS X is the answer.. but do you know what the question is?..."*
----
NSDocumentController - It seems that this class is instantiated magically and behind your back (now, I do hate this), if your info.plist includes a document type entry and a class to handle it..  Where in the Cocoa documentation is this?.. don't bother. It's not. Anyway... if you connect a menu item's action , say New, to the FirstResponder in IB, it will still be grayed out until the document controller class is instantiated which won't happen until you edit the info.plist file.. 
----

*The creation of an NSDocumentControler by the document architecture is fully documented at* [http://developer.apple.com/documentation/Cocoa/Conceptual/Documents/Tasks/ImplementingDocApp.html], *specifically where it says "the Application Kit provides you with a default NSWindowController instance and a default NSDocumentController instance."*
----
But could this be any more vague? :-)  "the App kit provides you with these instances".. as in "yeah.. they exist and you can use them in your programs".. but there is no instance of NSDocumentController you can see in any of the nib files created with the "Document based cocoa app" template in XCode, nor this is visibie from the source code.. I thought the document controller was the application delegate (as the documentation suggests).. but again, in IB, the delegate outlet is not connected..  anyway.. 

So according to the documentation, everytime you call NSApplication you get instances of NSWindowController and NSDocumentController automatically created?.. I don't think so since a) it says elsewhere that NSWindowController is instantiated by NSDocument, and if I am not doing a doc based app... well.. why do I need an instance of NSDocumentController for, etc, etc...

----

The 'for free' NSDocumentController and NSWindowController instances are created by NSDocument, not NSApplication. [http://developer.apple.com/documentation/Cocoa/Conceptual/Documents/index.html] has a pretty thorough description of what's going on. The NSDocumentController isn't instantiated in the nib because it doesn't need to be. You can access it from anywhere with     [NSDocumentController sharedDocumentController]

*Also, in your     main() function, that call to     NSApplicationMain() does a lot of stuff. There's no magic here. There doesn't have to be.*

