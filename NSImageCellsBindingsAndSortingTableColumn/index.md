---
layout: page
title: NSImageCellsBindingsAndSortingTableColumn
---



In a bindings-enabled table view I have a column of NSImageCells. I naively bound this column to an NSImage key in the model object that supports the table. Of course, when I click in the column header cell, the array controller attempts to sort the table based on that key and the app emits this error:

    
2005-03-04 17:23:27.266 SomeApp[7646] *** -[NSImage compare:]: selector not recognized


This behavior is simply unacceptable. Not to mention, embarrassing.  After puzzling about this for awhile, I can think of several ways to overcome this


*1) Somehow disable the sorting action transmitted by the header cell
*2) Implement an NSImage subclass that overrides compare: to do something reasonable
*3) Abandon the lovely image cells for stark, easily sortable text. This would be sad.


(1) seems easiest to me - that is, if it is possible at all. What else has been tried when solving this problem?

I'm onto http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaBindings/Tasks/images.html and looked through it for the answers, but it does not address this issue.

----

I think this is more of a sorting problem, rather than an image problem. You can add a custom sort descriptor to your array controller to handle images and any other unsorted types. Check out NSSortDesciptor and the Apple documentation on sort descriptors at http://developer.apple.com/documentation/Cocoa/Conceptual/SortDescriptors/Concepts/Creating.html . --JediKnil

----

Thanks, JediKnil. This is leading me on an interesting chase I have not completed. So far, I am finding Apple's documentation of NSSortDescriptor profoundly opaque in explaining how to apply it to my images, which have a definite sortable order.

The quick-and-easy solution to my table-sorting problem turns out to be ridiculously simple, since the set of images is one-to-one with a sortable set of value ranges in an adjoining column. What I have done instead is to set the sort key for the column of images to be the **same** as that for the column of values that I *actually* want to use to sort the table when the user clicks the column of images. I am assuming this key I set is actually the model key, and not just the identifier for the table column, which of course uses the same string as the model key.

I hope the above description is more or less comprehensible. This solution works only because the images represent and augment values in another column of the table. If I needed to sort the images independently of other columns, I would have to fall back on NSSortDescriptor, but I haven't seen any usable examples of how to install one of these inside my array controller. If I do this for one column, would I have to do it for all columns that I want to use to sort the table?

----

Sort keys are indeed model keys, not column identifiers. So if you need to sort by something that's not a column, that's ok, you just need an accessible model key that represents the sorting order.

