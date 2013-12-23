---
layout: page
title: RefreshNSToolBar
---

Hi.

I have a toolbar in my app, one of the NSToolBarItems is used like a button. This is great - NSToolBar is doing a great job. However, I want this NSToolBar item to be able to change its displayed title string (and its icon too hopefully) because it toggles a process on and off - and I want the button to show the status of the process. (ie. I want the text of the NSToolBarItem to toggle between "Start process" and "Stop process"). Unfortunately, I can find no way of forcing the NSToolbar to refresh.

I put code in - (NSToolbarItem *)toolbar:(NSToolbar *)toolbar itemForItemIdentifier:(NSString *)itemIdentifier willBeInsertedIntoToolbar:(BOOL)flag

(just an "if... then" which changes the title depending on the state of the above process) which correctly gives the right title for the NSToolbarItem, but only when it's a new 'ToolBar button' being dropped off the customisation pallette. How can I update the toolbar so that the ToolbarItem shows what I want it to at that specific moment in time?

Genuine thanks for any help offered,

-Peter 

----

Put the code in your toolbar's     validateVisibleItems method

