---
layout: page
title: NSDataRelated
---

Hi
I need multiple windows in my application. The first one I would like it to be a regular NSWindow. When a button is hit, pop up another window, which should be NSDocment windows.  

Anyone knows how to have a NSWindow pop out BEFORE  an NSDocumentWindow  is load up? For this application, is it neccessary to be a Document-based applications?(or I can just use a NSDocument subclass?)

Thanks for help,
newToCocoa

----
What does this have to do with NSData?

Yea you need a doucument based application....infact there is no such thing as a NSDocumentWindow. I would explain how you would need to create a window in the mainmenu.nib file that is set to view on launch and supress document creation at startup, but I think you would be better off if you just read a good book. Look through the CocoaBooks section of this site.
----
Sorry, I admit that I am kind of confusing after read the book of " Learning Cocoa with Objective-C". I exercised through building up 2 windows(actually one window and an info. panel) and made them all work. But I still could not figure out how to "create a window in the mainmenu.nib file that is set to view on launch and supress document creation at startup". Could you please kindly explain to me?

Many thanks,  newToCocoa

ps. I should of change the title of this discussing. I was thinking of using NSData later on in order to analysis files. Sorry .

----
Create a new Document based application.

Open MainMenu.nib, add a window and click visible on launch in the inspector.

Add this function to the application delagate:
    
-(BOOL)applicationShouldOpenUntitledFile:(NSApplication *)sender 
{ 

return NO;

} 


Perhaps that might help, but my original advice still stands.

