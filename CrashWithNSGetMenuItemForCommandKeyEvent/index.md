---
layout: page
title: CrashWithNSGetMenuItemForCommandKeyEvent
---



Two users have sent me identical crash logs, they look like this:
    
Exception:  EXC_BAD_ACCESS (0x0001)
Codes:      KERN_PROTECTION_FAILURE (0x0002) at 0x00000009

Thread 0 Crashed:
0   com.apple.HIToolbox     	0x927d11ec HIObject::GetHIObjectFromRef(void*) + 0
1   com.apple.HIToolbox     	0x927dc138 GetMenuItemSystemAttributes(MenuData*, unsigned short, unsigned long, unsigned long*) + 0x68
2   com.apple.HIToolbox     	0x927e6270 GetMenuItemAttributes(MenuData*, unsigned short, unsigned long*) + 0x20
3   com.apple.HIToolbox     	0x9284b878 SearchCacheEntries(OpaqueCollection*, unsigned long, unsigned long, CheckMenuData*, MenuResult*, MenuData**, unsigned long*) + 0x88
4   com.apple.HIToolbox     	0x92811ec8 SearchCache(OpaqueCollection*, bool, CheckMenuData*, MenuResult*) + 0x144
5   com.apple.HIToolbox     	0x9284a008 CheckMenuKeyCache(MenuData*, CheckMenuData*) + 0x150
6   com.apple.HIToolbox     	0x9280c7c4 CheckMenusForKeyEvent(MenuData*, CheckMenuData*) + 0xcc
7   com.apple.HIToolbox     	0x928189b0 IsMenuKeyEvent(MenuData*, OpaqueEventRef*, unsigned long, MenuData**, unsigned short*) + 0x228
8   com.apple.HIToolbox     	0x92828a6c IsMenuKeyEvent + 0x8c
9   com.apple.AppKit        	0x92e71614 _NSGetMenuItemForCommandKeyEvent + 0x94
10  com.apple.AppKit        	0x92e2e700 _NSHandleCarbonMenuEvent + 0x1f8
11  com.apple.AppKit        	0x92dd25cc _DPSNextEvent + 0x4d0
12  com.apple.AppKit        	0x92de8c24 -[NSApplication nextEventMatchingMask:untilDate:inMode:dequeue:] + 0x74
13  com.apple.AppKit        	0x92dfcf8c -[NSApplication run] + 0x21c
14  com.apple.AppKit        	0x92eb948c NSApplicationMain + 0x1d0


Both users cannot reproduce 100%, and one says the problem is now gone. I really have no idea what's wrong, so if anyone has the slightest clue of what might be the problem here, your help would be much appreciated!

I do gather from the crash log that it has to do with the system going through the menus to match a key event with the key equivalents stored for each menu item. But what could be wrong with the menus to make it crash?

I do set myself up as delegate for one menu and supply a submenu on request. The code to construct the menu is rather simple and I do provide either a key (as a string) or the empty string as key equivalent (not nil).

----

Can you post code? How are you supplying the submenu (i.e. are you retaining it or is it autoreleased?)

----

I can post code, but I'm not sure which. The code that builds the menu is trivial and I do not create the menu, the system already has a menu created when it calls the menu item delegate, and I just use:     addItemWithTitle:action:keyEquivalent: to fill it. I do add submenus for some of my items, these are auto released.

I'd hoped that someone would recognize the stack dump -- maybe a bug with OS X and menu delegates or similar? As said, this problem is randomly experienced by two users out of probably thousands.

*Later: I'm writing this off as a framework bug. What seems to be the problem is, that the system will use the sub menus from the menu controlled by the delegate *after* asking the delegate to update the menu (so the system will use un-retained menu items). If I retain+autorelease each item of the menu before I re-populate it, the crash seems to disappear.*

----

Well, coding like that is just good practice... Let this be a lesson to you ;)

*WHAT? I hope you're kidding. Should I also start to go through each item in an array and retain+autorelease before I dispose the array?*

----

Congratulations on catching it!  Sounds like one of the harder bugs.

