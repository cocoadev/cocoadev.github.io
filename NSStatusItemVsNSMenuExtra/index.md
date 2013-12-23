---
layout: page
title: NSStatusItemVsNSMenuExtra
---



NSStatusItem says 'The "proper" way of doing an NSMenuExtra.'

I was just wondering if anybody could clear up the differences between the two.

In particular, the HIG says:
http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/XHIGMenus/chapter_16_section_4.html
----
**Menu Bar Extras**

Reserved for use by Apple, the right side of the menu bar may contain items that provide feedback on and access to certain hardware or network settings. Menu bar extras display some type of status in the menu bar and have a menu to change settings. The icon for the battery strength indicator, for example, dynamically displays the current state of the battery for a portable computer, and the menu has common battery settings. Users can display or hide a menu bar extra in the appropriate preferences pane.

Important: Don�t create your own menu bar extras. Use the Dock menu functions to open a menu from your application�s icon in the Dock.

If there is not enough room in the menu bar to display all menus, menu bar extras are removed automatically by Mac OS X to make room for application menus, which take precedence. Because of this, and because users can choose to hide menu bar extras, you should not rely on their presence.
----

So does this guideline not apply to Status Items?  I ask because there are people at my company that take the HIG very seriously and are giving me loads of flack for wanting to do a Status Item.

Is there something in the newer Cocoa release notes about Status Items that supercedes the old HIG?

Also, I read that the Menu Extras all run as part of 1 program.  Is that not the case for Status Items?

And when there is not enough space on the menu bar, do Status Items drop off before Menu Extras?

Thanks for any insight you might be able to offer. -- JoelSmith

----
So nobody has any insight to offer on this topic?

----

I'd like to see some clarification of the issue as well if possible.  JKP

----

I believe StatusItems are A-OK to use. The main issue with MenuExtras if I am not mistaken is security, due to them being loaded into some system service as a plugin rather than running as their own process. A StatusItem on the other hand just overlays the menubar it seems and definitely runs from its own process.
----
StatusItems cannot get to the right of Menu Extras or be re-arranged like a toolbar either. There was a undocumented call for StatusItems to appear to the right of Menu Extras by setting its priority but this has been broken since. I suppose another advantage of having the StatusItem handled by a process is that if one goes down the others that may be running remain unaffected, but if the SystemUIServer goes down then the Menu Extras fall with it. Not sure if an individual Menu Extra can take down the SystemUIServer though.
----
Yes, unfortunately it can. My program, Dockyard, crashed my SystemUIServer a *lot* during development. I never thought about the security issue before but it makes sense; unlike the Dashboard (which runs each widget as a child process of the Dock), all of the menu extras are run by loading their bundles into the SystemUIServer. (This is why you need a program like Menu Extra Enabler just to get user-made menu extras into the list). Status items, on the other hand, are run as part of your own application, which need not have a Dock icon or any other interface. But when you get to that point it's basically a crippled menu extra; you can't move it around or run it without a full application (though again, your application doesn't have to have anything else). It would be nice if Apple upped NSStatusItem<nowiki/>s to the same status as NSMenuExtra<nowiki/>s, but for now you have to decide what's more important: behave like Apple's menu extras, or follow guidelines and keep your program within the realms of "documented and stable." For me, making it a menu extra, including reordering and everything, seemed to be the right choice. --JediKnil

