---
layout: page
title: DeallocNotCalledOnViews
---

I may be missing something obvious... but is there any reason the NSView subclasses in my project never get dealloc called on them? Is this some sort of shortcut for faster quitting behavior? I used to program for the Qt toolkit under linux, and there I had the option of having my app destructively shutdown, in which case all the views of a window always had their destructors called. Since Qt doesn't rely so heavily on delegation and controller design paradigms, but instead generally has the GUI component implementation handle its logic itself, it was important that destructors were called so I could save out settings when the app shut down.

Now, in Cocoa I have a main app controller class which is set as delegate for the NSApplication object, so it receives the applicationWillTerminate message (where I save out my app settings), but since my app is really large and complicated (it's a physics simulation environment for robotics) I have several controllers, set for handling logically distanct aspects of the GUI. Some of these are tightly bound to subclasses of NSView and it seems it would be convenient for me to simply put in code for saving out settings into their destructors.

I know that's probably bad design and I won't actually do that; I've instead just set up my main app controller to notify all the other controllers that the app's shutting down and they save out their state/settings. But, still, I can't help but wonder why NSView subclasses destructors aren't called.

Also, I don't think the destructor on my main controller is ever called either. What's up with that?

-- ShamylZakariya

You are right. When terminating an application destructors are not called for performance reasons. If you need to do some cleanup besides freeing memory, you either use the applicationWillTerminate: method of the app's delegate or register for an  NSApplicationWillTerminateNotification.

Quote from the documentation for NSApplication terminate:

"[...] this method posts an NSApplicationWillTerminateNotification to the default notification center. Don't put final cleanup code in your application's main() function-it will never be executed. If cleanup is necessary, have the delegate respond to applicationWillTerminate: and perform cleanup in that method."

--  AndreasMayer

----

It also says:

*The applicationWillTerminate: method does not get called for logout for power off. See NSWorkspaceWillPowerOffNotification, which is sent for these events.*

I am uncertain how I should interpret that -- I tried to store a value in the user defaults, and then shut down my machine, while my app was running, and the value did make it to the user defaults -- but according to the documentation, it doesn't seem that I can rely on this!?!

On a related matter, if I store values in the user defaults in the method mentioned above, will I have to invoke synchronize on the user defaults object? cause it says that settings are saved when the application exits, but is this before or after applicationWillTerminate:? I would assume that NSUserDefaults use the same notification, and thus the order is arbitrary, unless the delegate method is said to be invoked before the notification is sent...

