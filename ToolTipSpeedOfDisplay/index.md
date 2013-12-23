---
layout: page
title: ToolTipSpeedOfDisplay
---


I'm just wondering how you can control the speed of displaying of General/ToolTips. I'd like to have them be displayed instantly when you hover the item.

You could try this: http://cocoa.mamasam.com/COCOADEV/2003/05/1/63277.php

Thanks but i wonder how i'd use this in my application. My intention is to make tooltips for my nssstatusitem menuitems.. 
----
Tooltips for menu items are not very Cocoa-esque. Are you just going to use them to describe the items, or is there a dynamic component to them? Menu items should have descriptive names already, and not need extra description (which could go in a readme).

That said, I have no idea how to do this. --General/JediKnil

They are just going to contain some extra information.That's all.

----

Menu items does support tool tips. Why do you want the tool tip to appear immediately? This isn't what the user expects, and it may be annoying, e.g. if you just move the mouse slowly over the item, it'll popup.

...and likely obscure some other menu item, perhaps the one which the user was actually moving the mouse to.

----

What abt this?:

    
General/objc_lookUpClass("[[NSToolTipManager") sharedToolTipManager] setInitialToolTipDelay:0.1]; 


--General/RomanSabatini
