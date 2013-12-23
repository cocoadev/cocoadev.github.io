---
layout: page
title: FindLocationOfDockIconOnScreen
---



Hi everyone,
Suppose I have a dock icon, I would like to find the location, in screen co-ordinates of the top-right corner of the dock icon. My application has no visible windows.
Is there any possible method of doing this within the code?

Thank you
Saptarshi

----
Wow, there's an unusual request. I'm not condemning your goal, but what are you trying to do with it? It's easy enough to draw to your application's dock icon. The reason your request may be impossible (or at least very difficult) is because applications don't own their dock icons; the Dock does. --JediKnil

----
Well actually, i was trying to write something like xeyes, i could do it in the Statusbar(one option) or in a separate window(another option) but i thought it would be cool to animate the eyes in the dock icon itself. Well, if i want to implement the first option, then i need to know the location of the dock icon. I tried class-dumping dock.app but it reveals precious little (nothing).
Oh well.

----
What you probably want is     [NSApp setApplicationIconImage:someNSImage]. You can keep doing that to animate it, but I'm not sure how good the refresh rate would be. In general, finding the coordinates of another app's UI element is pretty prone to error or at least serious lag, if you can even find a reliable way to do it. --JediKnil

----
Thanks,I managed that and the mouse tracking(using GetMouse() and using a NSTimer - not sure if this is most efficient) but consider a the mouse at a fixed point in the center of the screen, if the eyes icons is to the left of the mouse, the eyes should look right and if the eye icon is to the right of the mouse, the eyes should look left. For this i need the location of the icon in the dock window - Saptarshi

----
Now I do not know how to do this exactly. But this may help you. Each icon in the Dock is it's own window. Using this you may be able to find it. Another thought is you could have something you know... like a pattern on the dock icon. You do a screenshot and search for that pattern to get the location. Random Thoughts --DanSaul

----
    
int processWindow;
CoreDockGetProcessWindow(&processWindow);
CGRect rect;
CGSGetScreenRectForWindow(_CGSDefaultConnection(), processWindow, &rect);
NSLog(NSStringFromRect(*(NSRect *)&rect));

The code simply prints the frame of the window for the current process. It uses private functions, but I think that's what you were expecting anyway. Look at CoreGraphicsServices, or http://alacatialabs.com/files/CoreDock.h if you want to know more about those.
To get (some? all?) updates to the Dock, register as an observer for the notification "com.apple.dock.prefchanged" using NSDistributedNotificationCenter. -- QwertyDenzel
----
To QwertyDenzel , the code you supplied is really neat! Thanks so much. Using a timer, i can also get the size of the dock icon, in realtime, even when the mouse is moving over and magnification is  turned on. Thanks -- Saptarshi

----

----
to the one fella who wrote the code:
Is there a way to update the Dock application without restarting it, like Apple does when the user changes the preferences for the Dock like magnification or the likes??

Thank you,
--MatthiasGansrigler

----

I don't have any idea if this would work, but have you tried touching the dock's preference file? If it watched it, that might convince it to reload. -- RobRix

----

Have a look at the new DockPrefsPrivate page. -- QwertyDenzel

----
The code above doesn't seem to work anymore, returns      { {inf, inf}, {0, 0} }  (running Leopard). Any update on this?

