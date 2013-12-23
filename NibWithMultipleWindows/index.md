---
layout: page
title: NibWithMultipleWindows
---

This will be an easy question.

If I have an app with one nib and in that nib I have more than one window, how do I refer to those different windows in code, ie. to show, close, etc.?

The only examples I've seen show how to have a 'preferences' panel, which is linked to the preferences menu option.  I would like to have my nib's controller object deal with showing/hiding the other windows of the nib.

By the way, in Interface Builder I can add menu items to the existing main menu items, but how do I add top level menu items (right next to 'file, edit, windows, help', etc.)

Thank you for any and all assistance,

Phil
----


*Add an outlet to your controller object in IB and name it something like auxWindow.
*connect the outlet to the second window
*save the nib file (very important)
*add the IBOutlet to your controller class in PB


to display auxWindow send the following message:

    
[auxWindow makeKeyAndOrderFront:self];


to close the auxWindow sent the following message:

    
[auxWindow close];


the close method call doesn't release the window, so to open the window again in code just send the makeKeyAndOrderFront: message again.

To add a top level menu item, a quick and dirty way to do it is make a copy of one of the preexisting menu items and then delete all of the items inside the top level menu item. To do this just option click on one the menu items (i.e. "Edit"). A copy of "Edit" will appear. Just delete all of the items in the Edit menu and rename the edit menu to whatever.

--zootbobbalu
----
Thanks, man.
----
Is there a way to expand loading a single window from a nib so that the window can be opened multiple times? 

    
[auxWindow makeKeyAndOrderFront:self];


That line only creates one instance of the window ever, and assigns it to auxWindow.

I'd like to be able to have many instances of the auxWindowClass all having a separate window.

Thanks for any help.

GregWilson 

----

hey Greg,

there isn't an auxWindowClass, auxWindow is just a window that was added to a nib (NextInterfaceBuilder) file in Interface Builder. Phil just wanted to know how to open and close a window that is added to a nib file. 

the line
    
[auxWindow makeKeyAndOrderFront:self];

doesn't create anything. This line only sends a message to a window that is part of a nib file. 

If you want to add more windows to a non-document based application then all you have to do is add the windows in IB. If all of this still sounds foreign, you might want to check out the difference between a document based app and a non-document based app.

--zootbobbalu

----

I want to be able to create many instances of the same window. However, it isn't the main window, nor is it a document window.

I've figured out that if the window is in a separate nib file I can instantiate it multiple times, however it doesn't return a class object that the main window can use to communicate with it.

I'm sure someone, somewhere has encountered this.

GregWilson

----

See NSWindowController (particularly Apple's reference docs for it, and maybe someone who's actually used it more than once) for exactly this purpose; basically, you make your window in a new .nib and have NSWindowController (or custom subclasses thereof) load that .nib. After that it's as simple as making new window controllers also with that .nib, and you get all your new windows "for free." -- RobRix

----

I feel stupid 8-) 

--zootbobbalu

----

For what it's worth, I think Apple should allow prototyping based on a top-level object in a .nib file, and I think controls should be allowed as top-level objects... anyhow, no worries. At least you know now. -- RobRix

----

Thanks for all your help. After I got it working using a separate nib file, I was still having a problem where the window objects weren't mapping to class variables. Finally found out that I needed to change the File Owner's class to the subclassed NSWindowController, rather than an instance of my subclassed NSWindowController.

Now it works great!

GregWilson

----

Whoops, shoulda mentioned that, sorry. Glad you got it worked out, though; should this page now be refactored as an article-type page? -- RobRix

Yes. Have fun. -- KritTer

----

Heh, thanks for this!  I was tearing my hair out trying to work out where to send the method makeKeyAndOrderFront to.  Tried [NSApp makeKeyAndOrderFront: myWindow] and it was spewing out errors.  Didn't think to try [myWindow makeKeyAndOrderFront: self] did I?  For some reason I have a mental block that stops me getting the idea of telling an object to do something itself!  Hope someone else who has this problem finds this too!  

Cheers!

