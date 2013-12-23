---
layout: page
title: DynamicApplicationMenuTitles
---

How do I change my application menu titles on the menu bar dynamically?

Based on reading the Cocoa AppKit documentation, I thought I could get the main menu using NSApplication's mainMenu method. Then I sent itemArray to the main menu. Then, using an iterator, I sent setTitle to each item in the array.

The code compiles and links without error. At run time it produces no error message, and the entire program runs correctly, EXCEPT the titles on the menu bar don't change.
Using the debugger, I can tell that the number of items returned by itemArray for the mainMenu is the number I expected to see.

----

This topic is a few months old, so I'm hesitant to edit it. But, since I found the answer, what harm can it do?

The only way I have found to set the title of an application's menu is by changing an entry in its info.plist file. Specifically, it's the value for the key CFBundleName you want to set to the new name (modifying the info.plist is an exercise left to the reader... but it involves NSBundle and NSMutableDictionary). Like the original poster, every attempt to so so programmatically compiled and ran without error, but totally failed to have any effect on the menu.

Caveats:

* does not take account of localisations. 
* can only be used to set the menu title on startup - I have no idea how to change the menu title dynamically while an app is running.


Cheers

Tom

