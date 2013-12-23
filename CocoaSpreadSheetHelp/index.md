---
layout: page
title: CocoaSpreadSheetHelp
---

There is a spread sheet class being worked on, by Tim Ritchey. This is the announcement he sent to macosx-dev@omnigroup.com (-- DavidRemahl):

I have put up a disk image of a first cut at a spreadsheet control at:

http://homepage.mac.com/tritchey

I am calling this the "who needs stink'n documentation" release.

To install, drag the RRSpreadSheet framework somewhere sensible, such as 
/Library/Frameworks and RRSpreadSheet.palette to /Developer/Palettes. 
You will need to add the palette the next time you run IB. Go to 
[Tools->Palette->Palette Preferences...] and hit (Add...). There is a 
folder called testspreadsheet that has a very simple PB project to show 
how to use the spreadsheet.

Right now, only the most basic functionality is there to access the 
spreadsheet data; however, following the release early, release often 
paradigm, I am letting it loose so anyone that is interested can start 
sending me feature requests. It should allow me to better place 
priorities on what I am doing.

A note on the final release license/cost: I am going to try and go for a 
three tier structure for this control:

a) it will be free for personal/education/freeware use.
b) for those interesting in using it for shareware, the charge will be 
the cost of a single license of the shareware product it will be used in.
c) A per-seat developer license fee (to be determined), but no 
royalties, for commercial use.

Cheers,
tim

----

I want to create a version of NSTableView whereby I can individually select cells - by this I mean not select whole rows, so that if my NSTableView consisted of 4 rows and 4 columns the user could select/highlight the center 4 of 16 cells (the intersection of the 2 middle columns and the 2 middle rows). Is it possible to do this? 

How would one go about getting this behavior? The reason I want to do this is I want some spreadsheet like capability for my users, like being able to fill right. But I don't want to go to a NSMatrix necessarily as I want all the other features of NSTableView, like the scrolling, user drag-reorderable columns, etc.

----

I think you would be best off writing a custom view rather than fighting NSTableView to do this. It wasn't really meant for spreadsheet-type use. (Scrolling isn't a property of NSTableView, either.)

----

The only cocoa spreadsheet app I know of is Mesa3 http://www.plsys.co.uk/mesa.htm, and it uses a custom view that does not descend from NSTableView (checked with FScriptAnywhere).

