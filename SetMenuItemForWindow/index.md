---
layout: page
title: SetMenuItemForWindow
---

I'm trying to create a itunes-style window menu. The Menu Item representing the main window should always be on the Menu, clicking on it would toggle the window visibility, and it should have an icon next to it when window isKeyWindow and isMiniaturized.

I know I could add a NSMenuItem to the Window menu, and call setExcludedFromWindowsMenu: for the window and to add the icons I could just implement validateUserInterfaceItem: and set the appropriate icons. But I'm sure iCal and iTunes don't do that, because the diamond icon it's not represented by any button state (correct me if I'm wrong). Moreover, since the window it's in its own nib, iCal should've set the window delegate to the nib file owner and every time it gets miniaturized the delegate should pass a message to the App Controller in the main nib and set the diamond icon, and IMO I don't think it does that, it's just to tedious.

NSApplication provides us with a few methods to manage the Window Menu:

� windowsMenu  
� setWindowsMenu:  
� addWindowsItem:title:filename:  
� changeWindowsItem:title:filename:  
� removeWindowsItem:  
� updateWindowsItem:  

But none of them let us to change the window's menu item.

I wonder how Apple engineers really implement this.

----

Scratch my previous reply, seems it's not supported on Mac OS X. So possibly those apps you mention are using undocumented methods (quite probable, Apple are alone in having that privilege) or they are dropping down into the Carbon menu manager which still (AFAIK) does support using the diamond checkmark symbol. Note that all menus on OSX are "really" Carbon menus anyway, so if you can't find the API you need in Cocoa, try Carbon. ~GrahamCox.

----

Yes, Carbon is the only option. Unfortunately the only (easy) way to get a MenuRef is     _NSGetCarbonMenu(), which is undocumented (and didn't work for me when I tried it, but I admit I didn't play around with it much). If you decide to go that route anyway, don't forget the bullet point that appears for edited documents too.

