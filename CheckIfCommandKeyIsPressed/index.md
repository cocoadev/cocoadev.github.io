---
layout: page
title: CheckIfCommandKeyIsPressed
---



Is there any simple way to determine if the command key is being held when selecting a menu item?  Thanks in advance.  (newbie).

----

Try this:

    
BOOL commandKeyDown = [[NSApp currentEvent] modifierFlags] & NSCommandKeyMask;


If you're a newbie, though, be a little careful.  You should have a good reason for changing behavior based on this, because it's kind of exotic.
----
You might also have some problems if the user tries to use the Command-<key> syntax to choose a menu item (e.g., Command-S for save). Maybe you should look into alternate menu items, which only show up if a modifier key is pressed. For an example of this, open the Window menu (shows up in almost every application), and press option; the Minimize option should change to Minimize All. Apple's docs are a bit lacking, but check out     setAlternate: at

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSMenuItem.html

(which is the best page on the ADC site I could find in 2 minutes). To use alternate menu items, just go into Interface Builder, put your alternate menu item below the normal one, change one of the modifier keys, and click "Treat as alternate to the previous item" (there's instructions there too). Overall, I think this is probably a better solution than checking a specific event mask, especially the Command key. But if someone out there knows better, please correct me. --JediKnil

----
The original example doesn't work, as ( modifiers & mask ) is truncated when copied into the BOOL.
A working alternative is:

    
BOOL commandKeyDown = (([[NSApp currentEvent] modifierFlags] & NSCommandKeyMask) == NSCommandKeyMask);


----

If you need to get the Command key state asynchronously (e.g. when your app is in the background and is not receiving keyboard events, or during preflight before the Cocoa event loop starts) you can use the Carbon function GetCurrentKeyModifiers:

    
BOOL IsCommandKeyDownRightNow(void) {
	return 0 != (GetCurrentKeyModifiers() & cmdKey);
}

