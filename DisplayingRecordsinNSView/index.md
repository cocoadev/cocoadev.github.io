---
layout: page
title: DisplayingRecordsinNSView
---



I wrote a program in another IDE (realBasic). I used a view (rb's canvas) to display records for data entry. I had roughly 5 records displayed in the view at a time. It was scrollable, but I simply populated the static view with data that changed as I adjusted a scrollbar. It was not implemented like a true NSScrollView. I created hotspots that could open a pop up window, reveal an  textfield for data entry. These hotspots were statically placed in the view, so whenever I adjusted the scrollbar, the hotspots would invariably be in the same place.

I would like to rework the program in Cocoa, and I have been studying up on NSView and NSTableView. I saw an example called SubviewTableViewTester that utilizes custom views in a TableView. The NSTableView populates each row with a custom view. I also saw the MOViewListView example in the MOKit. However, the immediate question I have is this: How scalable are these solutions when you have upwards of 5,000 records? If my custom view that I used in a TableView had a NSTextView and a few NSTextFields in addition to some other controls, could I really have an NSTableView with 5,000+ instances of these views and not have the program crawl? Or is the approach conceptually wrong?

livinginlosangeles

----

I would recommend simply using the table view as it's intended. You can use popup button cells, etc., it's a lot more efficient with resources (your users will thank you), and it's "mac-like". Using the view-as-cell approach you referenced would be massively resource-intensive and is probably completely unnecessary. If you could post a link to a screenshot of your current approach, we might be able to give you a better suggestion.

----

I have included a screenshot of the application. I appreciate your comments about using NSTableView. I guess the most genuinely intuitive approach would be to create a scrollable view that contained all of my records. As I understand, NSClipView will only render those graphics that are visibile in the frame, correct? And if I included controls (like NSTextView) in my subviews, they would only be rendeded within the current frame as well?
http://farm1.static.flickr.com/145/429722941_ba129d4d17_o.png

----

I see the complications you're facing. If you want to replicate this in Cocoa, you probably will need to create a custom view. To handle the tiling (stacking), you probably would need  to use the view-as-cell example you referenced. To abate performance degradation, I would suggest some sort of limit on the amount of records that are displayed at once. The user is unlikely to need all 5000 records displayed in one big view as finding one of those records would be *incredibly* inefficient. The most Mac-like approach is to rethink your interface a bit so that the actual number of records displayed in this list are limited to the most relevant. If you really need a list that displays all the records, consider a separate table that just shows brief summary information (as in the master / detail view design approach, for example).

Regarding the clip view, yes, it will only cause what's on-screen to draw (unless you've poorly written your view's drawing logic) but that's not the issue. Creating the views to begin with will most certainly be a computationally expensive task. Even if you created them only as they were needed for display, grabbing the scroll bar and scrolling quickly through your list would require some very quick work as well, resulting in a jerky, horrid UI experience.

----
A custom NSCell would also be something to consider. It would allow using a standard NSTableView and would be practical to make, although somewhat more complicated than a single custom view would.

----

... *quite a bit* more complicated in this particular case, I imagine. :-)

----
I think a custom NSCell might be a bit impractical, because you'd have to render all the text yourself.  Since all your rows are the same, you'd be best off by creating another nib and putting a custom NSView in it containing all the elements of one row (with placeholder text).  Then, put an empty NSScrollView in your document window, and programmatically (in your document window's delegate) instantiate the view archived in your secondary nib and add it as a subview of the content view of the NSScrollView.  This is how the predicate editors work in Apple applications (you can get a pretty good idea if you just open up Finder's or iTunes's nib files).

----
There's nothing preventing you from using an NSTextFieldCell or two inside your custom NSCell. It's a bit less convenient than doing the equivalent with NSView because there's no automated position tracking, invalidation, drawing, etc., but it's really not that hard to nest cells, you just override the draw/mouse/etc. methods and pass them on to the nested cells.

----
Point taken, but would you still want to do all that?  Apple sure didn't... ;-)

----
This problem has complexity regardless of the approach taken. The question is whether using an NSTableView with a custom cell will be easier or harder than using a completely custom view to accomplish the same thing. I personally believe that the NSCell/NSTableView approach will be easier and more scalable, although I'm not too confident about it. In any case, you can't just dismiss the idea as being too complicated because the other way is fairly complex too.

----

Nobody's dismissing anything, but as someone who's done something similar using both approaches, I can say that for this particular case, a custom view with the view-as-cell table example (first posted on stepwise.com) will be by far the most straightforward.

----

Thank you for the direction. I downloaded the NSCell documentation last week, read while watching Lost. I think the most important information is that I need to rethink the UI. I have a habit of replicating what I already know, ie filemaker.

----

Making your Mac OS X application look like a FileMaker "solution" will guarantee you a place in the lowest ranks of any software in your class. I promise. :-) That's not meant to be unfriendly, but brutally honest, given the high expectations of Mac users. I recommend taking a look at other applications (the "big boys") such as iTunes, Delicious Library, iPhoto, etc. These all handle potentially huge record sets in a very nice way. It's possible to display everything at once in all these applications, but the default view is a pertinent subset of the user's complete library.

----

Your point is well taken.

----

I would probably use both approaches, cells and views. It looks like the first few rows could be done with the default cell and the last row with views. You'd have to format the strings (line breaks, min. - fancier attributed strings max) which could take a bit of effort but should still be lighter than that many views/textfields. hmmm.

