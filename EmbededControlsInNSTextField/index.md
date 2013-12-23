---
layout: page
title: EmbededControlsInNSTextField
---



Hello everyone,

In relation to the same project as FindingSubnet, I am looking for help embedding a control (specifically a small clickable icon (much like the magnifying glass in the search field)), inside of an NSTextField. This will be used to allow a user to click on an item and have it zip them to the right spot in the program, or entry in the XML file (as it were), without a lot of effort.  It would also allow for the content to remain editable, and prevent the interface from becoming too crowded.


Thanks for the help.


Regards,

ChrisGiddings


President, Ripple

chris.giddings **at** ripplesw.net

----

This example reimplements an NSSearchField from a NSTextField (and cell etc). Does more than you want but I think you can use it to figure out how to do what you want:
http://numata.aquasky.jp/programming/cocoa/NMSearchField_en.shtml

EnglaBenny

----
CCDTextField does this and was written because NMSearchField did a little more than I wanted. :-)

Usage is pretty easy too, assuming you've changed the class of     textfield in IB...
    
- (void)awakeFromNib
{
id leftCell = textfield cell] leftButtonCell]; // or rightButtonCell

     [leftCell setImage:[[[NSImage imageNamed:@"yourImage.tiff"]];
     [leftCell setTarget:self];
     [leftCell setAction:@selector(leftCellAction:)];
}


----

Does this also work with a subclass of NSSecureTextField. I tried this with other examples, but the icon never appeared :(

