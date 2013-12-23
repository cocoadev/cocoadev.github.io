---
layout: page
title: RegisterEventHotKey
---

Wondering if anyone could give me a clue about how to correct implement a Hot Key Handler (Using the Carbon RegisterEventHotKey event from Cocoa) that handles references to an NSSearchField object inside an NSWindow? The background is that I'm developing a small application in Cocoa using Xcode 2.2 on Mac OS Tiger 10.4.4. The application should be used to copy and translate selected text from any other MacOS X application by pressing a pre-defined hotkey. I�m using the Carbon event RegisterEventHotKey to trigger the hotkey and this part seems to work fine.  But the problem shows up when I would like to set a value in an NSSearchField after the hotkey have been pressed. * OSStatus MyHotKeyHandler(EventHandlerCallRef nextHandler,EventRef theEvent, void *myObjectPointer);*I can't refer any of the Object-C instances / methods defined in the Nib from MyHotKeyHandler, and retrieve a compile error if I try to use something like [phraseField setStringValue:@�xxx�]. As far as I understand, you can't directly use Object-C variables from here, like self etc.I've done my bindings	s

Sounds like you want to implement a System Service.

http://developer.apple.com/documentation/Cocoa/Conceptual/SysServices/index.html

You may want to check out this site: http://dbachrach.com/blog/2005/11/28/program-global-hotkeys-in-cocoa-easily/ 

I think this part of that tutorial fits your problem:

*An extra side note. I for one, have methods in other classes I would like to call once these hotkeys have been pressed. To solve this, pass in an object at the second to last parameter in InstallApplicationEventHandler. Then one you are in your case statement, you can do: [userData runMethod:somevalue].*


----
Please see these pages: HowToAskQuestions, HowToUseThisSite, MailingListMode, and last but not least TextFormattingRules. You will get a lot more out of this site if you learn to use it first, and put some effort into the pages you create.

