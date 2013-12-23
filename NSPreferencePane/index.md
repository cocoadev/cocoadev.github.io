---
layout: page
title: NSPreferencePane
---

http://developer.apple.com/documentation/UserExperience/Reference/PreferencePanes/ObjC_classic/Classes/NSPreferencePane.html
http://www.diggory.net/grazing/prefpanedefaulticon.jpg
----

A template class for subclassing to create your own prefPane.

notes:

You don't access the defaults normally in a prefPane - because that would get you the SystemPreferences app's prefs.   
Instead you want the persistentDomain for your bundle's Identifier.

The title of the Nib you get when creating your own prefpane bundle is <<DO NOT LOCALIZE>> - this refers to the title of the window - not the window itself.   SystemPreferences app changes the title - that's what the warning is for.

If you want a technique for debugging your prefpane see here:
http://cocoa.mamasam.com/COCOADEV/2001/11/1/17637.php [dead link, see http://web.archive.org/web/20041029230106/http://cocoa.mamasam.com/COCOADEV/2001/11/1/17637.php]

Apple's guide on the topic: http://developer.apple.com/documentation/UserExperience/Conceptual/PreferencePanes/

