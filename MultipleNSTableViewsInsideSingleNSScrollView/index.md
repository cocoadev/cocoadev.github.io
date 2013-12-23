---
layout: page
title: MultipleNSTableViewsInsideSingleNSScrollView
---

Sorry if this has been asked before. I searched for some information, but there is so much that it is easy to miss.
I was wondering how to go about putting two NSTableViews inside a single NSScrollView. I essentially want to have two tables
of the exact same height that are in sink together when scrolling, but there would be a divider between them separating them.

    
E.g.                                                                          Scrollbar
                                  BREAK                                      /\

--------------------------------- |       |----------------------------|       |
|                                |        |                            |     | |
|  TableView1                    |        |                            |     |  |
|                                |        |    TableView2              |     |  |
|                                |        |                            |    |  |
|                                |        |                            |     | |
|--------------------------------|        |----------------------------|       |
                                                                                 \/


EDIT: Sorry I could not get the picture correct, it appears much better in edit mode :(

Sorry for the poor drawing. Right now I have made a subclass of an NSView, set that as the document view to the NSScrollView and then I added my two table views to the new view, unfortunately the headers are now gone. There present if I directly set the table view as the document view of the scroll view though.

Thanks
----
Place and configure both table views in Interface Builder.  Select both.  Use menu Layout->Group in Scrollview or similar.  I am not able to ceck the exact menu name at this time.

Alternatively, make both table views subviews of the same custom view.  Group the custom view in a scroll view.

If you are trying to remove the scroll bars from the individual table views, the easiest way is to calculat the size needed to display all of both tables and set the table view's big enough so that they don't need scroll bars.  The scroll bar for the scroll view that encloses both table views will automatically adjust to reflect the size of the tables when you resize the tables.
----
Read the conceptual documents regarding view frame, bounds, and scroll views.

*What you want to do will likely be very hard to pull off; an alternative on the TwoPDFViewsWithSynchronizedScrolling page is to have one scroll view to scroll whenever the user drags the scroller on the other one.*

