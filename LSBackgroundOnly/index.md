---
layout: page
title: LSBackgroundOnly
---

*From Apple's docs:*

If this key exists and is set to "1", Launch Services runs the application in the background only. You can use this key to create faceless background applications. You should also use this key if your application uses higher-level frameworks that connect to the window server, but are not intended to be visible to users. Background applications must be compiled as Mach-O executables. This option is not available for CFM applications.

You can also specify the type of this key as Boolean or Number. However, these type values are only supported in Mac OS X 10.2 or later.

http://developer.apple.com/documentation/MacOSX/Conceptual/BPRuntimeConfig/Articles/PListKeys.html

See also LSUIElement.

----
LSBackgroundOnly (sometimes LSUIElement, I believe that's what it is, is also used, but for different reasons) is the flag for the active target that tells the compiled application to run in the background.

----

I believe that LSBackgroundOnly hides all windows and status items, which makes a true daemon app, whereas LSUIElement allows windows and status items. I could be wrong about this though. -- KentSutherland

----

Actually LSBackgroundOnly does remove the standard menus, however, the developer can add an extra menu(s) for whatever reason to be used in various ways, perhaps contextually, perhaps as an addition to another part of the operating system.  Further, as long as the LSBackgroundOnly flagged application forces a window to the front, then that window will be available, for example a global floating panel.  However, LSUIElement is typically used for user element based interfaces/applications, whereas LSBackgroundOnly is typically used for true background only applications.  As always, it is really up to the developer to decide which is more appropriate.  Of course, you should probably look into the variations on Apple's developer website.  

--TriLateral

----

