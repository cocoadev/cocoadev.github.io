---
layout: page
title: NSSearchField
---

If you haven't noticed there is now a NSSearchField in Panther.  This is the control that is used in the popular Apple apps (iTunes, Mail, etc.).  Unfortunately there seems to be no documentation yet.  Has anyone been able to determine any of the API?  And if so (being a newbie), how did you determine it?

Thanks,

EricFreeman

----

You can always look at /System/Library/Frameworks/AppKit.framework/Headers/NSSearchField.h. Presumably. I don't have Panther, and if I did I wouldn't be able to talk about it.

----

NSSearchField documentation is available at http://developer.apple.com/documentation/Cocoa/Conceptual/SearchFields/index.html

-- SashaGelbart

----

I am sure there is a replacement for NSSearchField out there that has all the features but runs on Jaguar too. The problem is I can't find it. :(

Does anybody hav a url? -- Martin H�cker

----

WBSearchTextField

Look in ObjectLibrary - [http://s.sudre.free.fr/Software/DevPotPourri.html]
----
If you want to have a Recent Searches menu, like Safari, you have a NSSearchField
and then you create a NSMenu, and connect the NSMenu to the NSSearchField as a searchMenuTemplate, then add three NSMenuItem's to it like this

*Recent Searches - Tag 1000
*Recents - Tag 1001
*Clear Recent Searchs - Tag 1002

The API handles the rest!

--JoshaChapmanDodson
----
NSSearchField menu templates are better explained here.

http://developer.apple.com/documentation/Cocoa/Conceptual/SearchFields/Articles/MenuTemplate.html

and here

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSSearchFieldCell.html

e.g. In code, the tags are NSSearchFieldRecentsTitleMenuItemTag, NSSearchFieldRecentsMenuItemTag, NSSearchFieldClearRecentsMenuItemTag, as well as NSSearchFieldNoRecentsMenuItemTag.
----
I tried, but still nobody wants to take my information. :(

=JoshaChapmanDodson

