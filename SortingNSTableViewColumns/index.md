---
layout: page
title: SortingNSTableViewColumns
---



This page is a resource for techniques used in sorting the contents of an NSTableView by row based on the sort order of one of its columns.

A table view displays the contents of its data source passively, using the informal protocol NSTableDataSource. Typically, the data are in something like an NSMutableArray, declared as an instance variable of the data source object. The data source of the NSTableView is an outlet of NSTableView, similar to  a delegate object.

In sorting the data source array, which typically is a container for other objects, you need to compare those objects. The venerable NSC**'omparisonResult is how you compare objects with each other in a general way, by returning it from a method that compares two objects in the array. To learn about the basic nature of object comparison, consult the documentation for NSObject and in partcular, the     equals method.

A simple example of sorting a table using this technique is found at SortingExampleUsingNSComparisonResult

Further information on sorting an NSArray using sort descriptors can be found at

http://developer.apple.com/documentation/Cocoa/Conceptual/SortDescriptors/Concepts/Creating.html

note that using setting sort descriptors in the nib file may break 10.2 compatibility

----

The actual details of sorting your data are wrapped up in the details of the array or dictionary that you use with your model data.

With CocoaBindings and such objects as NSArray controller, you have powerful new ways of implementing this functionality (see below).

Among the more "manual" methods that you have to sort an array or dictionary that contains your table data is
sortedArrayUsingSelector: or sortedArrayUsingFunction:context: and simply compare each object.

For further help on NSArray methods, see the numerous array-related topics to be found at this site.

----

One thing that users expect and enjoy is the ability to sort their tables by clicking on a column header cell. A simple example of how to do this is found at
SortingTableViewByClickingColumnHeaderExample

Since Jaguar, the icons that indicate ascending or descending sort order are now available as named images:

You can get these images with [NSImage imageNamed:] using the identifiers

@NSA**'scendingSortIndicator (^)

or

@NSD**'escendingSortIndicator (v).

(contributed by) /Armin

further related discussion about how to avoid using private methods (that might be removed) for sort arrows, see this link:

http://cocoa.mamasam.com/MACOSXDEV/2001/06/1/7790.php

----

With the introduction of CocoaBindings, a powerful technology for handling this responsibility is incorporated in NSArrayController

one article about this is found at

http://cocoa.mamasam.com/COCOADEV/2004/02/2/85255.php

