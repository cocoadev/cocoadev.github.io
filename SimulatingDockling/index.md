---
layout: page
title: SimulatingDockling
---

I've read the posts here on how Docklings have been deprecated by Apple.  I'd really like to simulate its functionality somehow, however.  

I already know how to add dock menus (there's a good tutorial at http://cocoadevcentral.com/articles/000036.php ), so that's fine.  But what I really want is to make it so that my app doesn't become active when the user clicks on it in the dock.  I want its only interaction with the user to be thru the dock menu.  There will be no application windows, so it will just be confusing if the user clicks on the dock icon and it brings up an essentially useless app.

Thanks for any tips!

----

Maybe you can make it not confusing.... show the user a preference pane or some other sort of configuration optionsif they bring it to the front(click on it), they'll understand that it's supposed to just be used from the Dock...

----

If the user will only be accessing the application through the menu, wouldn't it make more sense to use an NSStatusItem and have the application run in the background? Anyone have any input on using this as an alternative to docklings? Also see: NSStatusBar and NSMenuExtra

----

I originally was going to do that, but I've all but decided against it now.  I just don't think it's good developer practice.  On my 12" iBook, with the number of other ones I have on my menubar, often my menuextra did not even show up (in something like Word or XCode which has a lot of menus).  So I'd definitely, at the very least, have to have an alternative interface for my program, since it simply won't work all the time to put it in the menubar.

----

Well, you could hide the app every time your application delegate receives an     applicationDidBecomeActive: message, but I think this is a **very bad idea** and violates the way Mac applications work. Users expect something to happen when they click an icon in the dock - even the trash does something. I suggest creating a window that has the exact same functionality as the dock menu. Even if it is a bunch of buttons, it is better than nothing. You could then have the window come up every time you click the app. You could even make it an NSPanel that is only visible when it is the current application. With this way the user has the choice of using the menu or the window. -- RyanBates


----

Thanks for the advice.  I think this what I'm going to end up doing.  And I'll look into the NSPanel thing...I didn't know about this.

