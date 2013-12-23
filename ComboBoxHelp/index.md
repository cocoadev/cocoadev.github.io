---
layout: page
title: ComboBoxHelp
---

I could use some help with NSComboBox and auto-completion.. i'm getting this when i type a character that there is no match for in the dataSource

2004-01-19 01:16:28.974 GameCollection[4231] *** -[NSComboBoxCell length]: selector not recognized
2004-01-19 01:16:28.974 GameCollection[4231] Exception raised during posting of notification.  Ignored.  exception: *** -[NSComboBoxCell length]: selector not recognized

And i can't figure out where it's coming from, the method that gets called when it works is
- (unsigned int)comboBox:(NSComboBox *)aComboBox indexOfItemWithStringValue:(NSString *)aString
but once i type Xb for example (instead of XB to match XBox) 
the above message comes up...

anybody got ideas??

-- JeremyK
jerome at cursory dot ath dot cx

----

length is a selector that NSString recognizes, so You're probably sending the NSComboBox object somwhere, when you should instead be sending it's contents (an NSString) instead

