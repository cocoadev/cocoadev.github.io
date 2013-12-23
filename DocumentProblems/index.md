---
layout: page
title: DocumentProblems
---

Someone originally posted this over the HowToCreateNewPages page. I have reverted the page, and have created this discussion for the question.

----

I'm working on a document-based app and am looking for a straightforward way to modify the text of an item in one of the menus from  within a method belonging to the document or document controller classes. Attempts to instantiate the document controller object in MainMenu.nib (which would give it an outlet to the menu item text) have not yielded success. 

The difficulty is that the menu data is in a separate nib file from the document nib, making the usual IB-type connections for outlets and actions a bit more problematic. NOTE: I have no problem connecting the menu item in question to an action in the document class, using the usual capabilities of the responder chain. FURTHER NOTE: The document controller is connected to File's Owner in the document nib file. 

I hope this is enough background for someone to suggest an approach.
----
When I have to access global properties (like the application menu) I usually make my own NSApplication subclass and put them there. That way I can just ask NSApp for whatever I want.

For that to work you will have to change the 'Principal class' in PB's target settings (Info.plist entries - Simple View - Cocoa-Specific) from NSApplication to your own subclass. -- AndreasMayer
----
Subclassing NSApplication seems like overkill to me. Couldn't you just have the menu items' actions post notifications which would then be received and responded to by the document class? --Goldfish
----
If I read that correctly, he wants to access the menu items without activating them. Besides - what's the problem with a simple subclass? Of course for this special problem one could just get the menu item from NSApp directly:

    
- (NSMenuItem *)itemFromMainMenuForTag:(int)tag
{
   NSMenuItem *result;
   NSMenu *mainMenu = [NSApp mainMenu];
   result = [mainMenu itemAtIndex:[mainMenu indexOfItemForTag:tag]];
   return result; 
}

*(typed directly into Safari - no warranty whatsoever)*

There's also indexOfItemWithTarget:andAction: if you don't want to differentiate the menu items by tag.

 -- AndreasMayer

