---
layout: page
title: NSBeginAlertSheetIsIrritating
---

Hi,

maybe I am using NSBeginAlertSheet improperly, but it strikes me as very irritating to use in certain situations.

For example, I am using it to as a question of the user on ApplicationShouldTerminate... the outcome of which will determine whether that method returns NO (don't terminate) or YES (terminate).

In most languages you can do something like this:

    
- (int)applicationShouldTerminate...
{
return (Alert("Do you want to terminate the app"));
}


Whereas with NSBeginAlertSheet, I have to specify a selector for the outcome to be evaluated in... so instead I have another method just to evaluate the outcome of the NSAlertSheet, and it gets really confusing especially in this situation because you can have an infinite loop very easily...

1 ApplicationShouldTerminate called

2 Sheet Asks 'Are you sure'

3 User clicks yes

4 Sheet method reads the outcome of the sheet

5 User clicked yes so [NSApp terminate];

6 GOTO 1;

There must be a more efficient way to do this... am I using NSBeginAlertSheet where I am not supposed to perhaps, is there a different class to use?

----

I take some of this back after discovering NSTerminateLater and replyToApplicationShouldTerminate... but it still makes for a lot more code than in other languages (I've heard Obj-C and Cocoa were a little verbose, but this just seems irritating).

----

In most other languages/environments, this type of thing is application modal. Sheets aren't. You could always use modal panels. I personally perfer the flexibility using sheets provides.

