---
layout: page
title: BindingTableviewSortDescriptors
---

Im trying to save sort descriptors for a NSTableView created with Cocoa Bindings. I see there is an option to bind the sort descriptors in a table, but trying to bind the sort descriptors to a key name in the preferences results in some strange error message "attempt to insert non-property value" and for some reason Mac OS X keeps preventing me from saving the sort descritptors any other way. What are the best ways to save sort descriptors through Cocoa Bindings so they can be restored to a table upon the next launch of the app?

*Didn't someone just ask about this? See SettingArrayInDefaults...although this name is more descriptive.*

----

Sorry I didn't read the other one nor am I the same author. I guess this question is really more about how to store and retrieve sort descriptors. I've noticed in the prefs *.plist file for my app that it has a setting something like "NSTableView Sort Descriptors tablename" which is of the array type and appears to contain a NSData object and a BOOL for each sort descriptor which gives the appearance of  a way to store sort descriptors, though I don't know how to retrieve and decode the descriptors.

Im curious why theres so much information about how to enable sorting in NSTableViews, but very little information about actually saving + restoring sort descriptors? 


----


Um, did you read it now?  Because it answers your question.  Set an autosave name and it's automatic.

If you feel the documentation is not clear, file a documentation bug.

*Sorry, I'm yet another contributer to this post, and I can verify that this most certainly does *not* work for *me*. I'm having the same difficulties. It seems totally ignore the autosave name settings. Column sizes and orders are saved just fine, but not the sort descriptor. The documentation is clear, yes, but it's also clear that this isn't always the case. If anybody has filed a bug report please let me know the id number and I will add another voice and perspective to the list.* (Added: *It's also worth noting that this is occurring in two separate projects, both using straight bindings built in IB, no weirdness.*)

----
I'm having the same problem.. It was working before I migrated my app to use Cocoa Bindings. I have the same symptoms, table column size save works, sortDescriptors don't. Has anybody solved this? -- Paulo Andrade

----

When you bind your NSArrayController's sortDescriptors to Shared User Defaults, make up a Model Key Path to save your sort descriptors under, and be sure to set the built-in Value Transformer NSUnarchiveFromData. (And if you bound this earlier without the transformer, you may need to delete prefs.) Clue from: http://lists.apple.com/archives/cocoa-dev/2005/May/msg02224.html

