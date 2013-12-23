---
layout: page
title: NSComboBox
---

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSComboBox_Class/Reference/Reference.html

----

Combo boxes' pop-up list can be populated by a DataSource, or the combo box itself can store the list.

If the combo box stores the list, it always stores it as NSString's. If you apply a numeric NSFormatter to the combo box, nothing will show up in the pop-up list because the NSString's won't be formatted as numbers.

To use an numeric NSFomatter with an NSComboBox, the pop-up must be populated with a data source. The data source should return NSNumber instances.

The **-indexOfItemWithDataValue:** function does not work with a data source.

The Carbon equivalent is the HIComboBox. http://www.carbondev.com/site/?page=Combo+Box

http://google.com<nowiki/>scapeWordsJammedTogether

