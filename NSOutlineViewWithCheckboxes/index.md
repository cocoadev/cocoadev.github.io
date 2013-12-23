---
layout: page
title: NSOutlineViewWithCheckboxes
---

Is it possible to implement an NSOutlineView with (optional) checkboxes for each item?  Take a look at apple's installer app and how it displays the hierarchy of packages availible for install.  That's what I'm aiming at.  I am a cocoa / objective C learner at the moment, so I can't even imagine how to tackle it, I imagine I have to subclass.  Having had a look at the NIB file for Installer, It shows as a custom view.  

----

Yes, it is possible. There are two ways:

1. Add an extra column to your NSOutlineView, and set the cell prototype for the column to an NSButtonCell with the appropriate button type set. You can then set and use the values of the checkboxes just as you would the contents of any other cell in the NSOutlineView (in particular, in the body of your datasource and delegate methods).

2. Create a custom cell for your NSOutlineView, which includes both a checkbox and a space for text. It's roughly the same thing as having an NSOutlineView that displays text and icons. This is the harder way to do it - it requires considerably more detailed knowledge of how Cocoa objects respond to events and draw themselves. But actually getting it to work could be quite rewarding. 

----

The first option obviously sounds much easier, but isn't quite perfect in that there would be an extra column in the view.  Ideally I would like to go with the second option.  Is there any way you could give me a bit of a bump start with some example code of how you might start?  Or even just tell me what topics I need to research more thoroughly - I am heavy on the research at the moment so a bit more won't hurt!

----

....some time later...;)

A bit of digging and I turned up this which seems pretty similar.  http://www.cocoadev.com/index.pl?NSTableView.  The link at the bottom has a good looking tutorial on how to achieve a similar thing with tableviews, so I guess all I would need to do is use an NSOutlineView as the main view instead.  I need to look through the code so I can understand it properly, and also, I think i would want to try and adapt it to use cocoa bindings.

Am I on the right tracks?
----
Try looking at file:///Developer/Examples/AppKit/DragNDropOutlineView/ It shows a lot for NSOutlineView, including Checkboxes in the view.

~/JoshaChapmanDodson
----

I was interested in solving exactly the same problem starting from the same point: as an XCode/Cocoa newbie. I figured out how to 
accomplish the first option: see ButtonsInTableOrOutlineViews. I'm still looking at how to accomplish the second option.

----
....not long after....;-)

I discovered that there's a simple trick for accomplishing a variation on the second option that is nothing more than a simple variation on the first option. Using the ButtonsInTableOrOutlineViews example as a starting point, put the button cells in the first column instead of the second column. The button text labels can be used as the text fields. While this mimics the capabilities of Apple's Installer program, this basic approach doesn't support user editing of the text field.

