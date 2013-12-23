---
layout: page
title: NSApplication
---

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSApplication_Class/index.html

NSApplication inherits from: NSResponder: NSObject

----

An NSApplication object manages an application's main event loop in addition to resources used by all of that application's objects.

NSApplication has some useful delegate methods that allow customisation of an application's launching & quitting behaviour.

To have your application quit when there are no windows open (which should only be used for single-window desk accessory-type apps, never document based apps - think Calculator, not TextEdit) you can use the NSApplication delegate method:

    
-(BOOL) applicationShouldTerminateAfterLastWindowClosed:(NSApplication *)theApplication
{
    return YES;
}


you can also do some interesting stuff with NSApplication like change the application's icon. (like iCal)

Pass your image to     [NSApp setApplicationIconImage:(NSImage *)theImage]


----

NSApp runs in     NSModalPanelRunLoopMode while waiting to exit or while calling **-applicationShouldTerminate:**. In this mode, queued selectors or timers won't usually get invoked. If you want them to be invoked, create them for the     NSModalPanelRunLoopMode mode instead of     NSDefaultRunLoopMode.

The run loop mode will go back to     NSDefaultRunLoopMode if you return     NSTerminateCancel from **-applicationShouldTerminate:**.

-- DustinVoss

----

Getting NSApp to quit your app good and proper is covered in TerminateOrStopGood.

----

After searching high and lo, I am desperate enough to ask here -- how do I "hook up" my controller to be a delegate to my app?  Specifically:  I have created my applicationShouldTerminate method (exactly as described here and in many other webpages), and declared it in myController.h along with the other methods, but it's just not getting called.  I'm sure there's something I'm supposed to do in IB to "hook it up", but for the life of me I can't figure out what!  Any advice would be much appreciated.  --DarelRex@gmail.com
----
My advice is to work through one or two introductory Cocoa tutorials.  The File's Owner in the MainMenu.nib is typically the application's shared instance of NSApplication.  Just drag a connection from File's owner to your controller instance and set the File's Owner's delegate outlet to your controller.  You can also do this programatically: [[NSApplication sharedApplication] setDelegate:myController];
----
Thanks!  I will try those.  (I have worked through the Currency Converter tutorial, but it doesn't cover this topic.)  --Darel
[UPDATE:  Worked great!  Thanks again.]

