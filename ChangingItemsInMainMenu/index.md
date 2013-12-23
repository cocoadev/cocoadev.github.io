---
layout: page
title: ChangingItemsInMainMenu
---



 
* Following the suggestion in one of the previous threads, I have created a document-based application with a fairly simple Main Menu.  The end user, however, thinks that the Main Menu bar is too cluttered and would like some of the menu items to "disappear" until needed at some other time.  To accomplish this goal, I have added a single pull-down menu that, in theory, should allow the user to toggle the existence of a different item in the Main Menu.  It is fairly simple matter to have the toggle menu item send an "action" to an NSObject subclass instance wherein the necessary operations take place to modify the Main Menu.  After having studied how to proceed for a couple of days, I am stuck.  How do I remove, and then replace, menu item in the Main Menu bar upon an action being sent from another pull-down menu?  This might seem like a really dumb question but the answer will help to tidy up the application.  Thank you for your assistance.


----

Google is your friend.
To add, [[NSApp mainMenu] addItem: theItemOwningTheSubmenu];
To remove, [[NSApp mainMenu] removeItem: theItemOwningTheSubmenu];
That said, dynamically changing the top-level menus in the menubar is considered bad form according to the HumanInterfaceGuidelines.

----

I must agree - sounds like a Windows app. That said, if the "end user" is the only customer, fair enough. But if it's going out to a wider audience, reconsider - Mac users don't like menus changing dynamically too much, because it subverts one of their main purposes - a stable 'road map' of your application. --GC

----

Agreed, if this is for distribution -- if just one client then the client is always right :). But to give the OP a direction to proceed in, the answer to a cluttered Menubar is to think of reordering and consolidating items in the menus, using submenus to group related tasks, or even offloading lesser or contextual functions to palettes or floating bars. That's much more the Mac way and is better in anycase. If there is time, give this approach a thought. -- GA

----

Thank you for your well-considered responses.  I agree that changing the top-level menu is poor form, but the customer is always right unless he can be gently persuaded that an alternative would be more effective and comfortable.  I like the idea of a "floating menu bar."  I'll think about this and come back to you latter with my insights.  --NB--

