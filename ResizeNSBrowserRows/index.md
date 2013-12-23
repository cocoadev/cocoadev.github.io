---
layout: page
title: ResizeNSBrowserRows
---



I have an NSBrowser in which I would like to display larger (64 x 64) icons along with the usual text for selection. Is there are way to change the height of the rows in the NSBrowser view so that the large images fit?

I have tried playing the matrix object given specified in the NSBrowser delegate with     browser:createRowsForColumn:inMatrix:, but have had no luck:

    %
- (void)browser:(NSBrowser *)sender createRowsForColumn:(int)column inMatrix:(NSMatrix *)matrix {

     [ matrix setCellSize:NSMakeSize(100, 50) ];
     [ matrix setIntercellSpacing:NSMakeSize(10, 10) ];

}


I'm sure this kind of thing is possible - have I missed something?  ---TheMZA

----

One way you could do this is by subclassing NSMatrix and telling your browser to     setMatrixClass: to your NSMatrix subclass. Then override the NSMatrix instance method     cellSize to return the size of the cell you wish the browser to display. 

    
- (NSSize)cellSize {
    NSSize size = [super cellSize];
    size.height = 30.0f;
    return size;
}


By asking     super for the size first you don't have to worry about setting the width of the cell. The only problem with doing this is that you will probably have to subclass an NSBrowserCell to do custom drawing for your custom cell height. Default icon placement and each cell's title will not align with the modified height returned by the call to     cellSize. 

To get an NSBrowser instance to incorporate your custom cell, you have to create a prototype cell and return this prototype cell in NSBrowser's instance method     cellPrototype.  --zootbobbalu

----

Excellent - that worked a treat! Many thanks!  --- TheMZA

----

**More insight about NSBrowser row height, moved from NSBrowser Vertical Spacing (unjammed together)**

I cannot for the life of me figure out how to change the amount of space between rows in my NSBrowser. Can anybody shed some light on this?

----

NSBrowser is a "special" kind of control. its one of those NSControl classes that has Other NSControl classes inside it.
NSBrowser is made up of separate Matrix Views, populated with NSCells. 

NSBrowser is designed to "mostly" just pay attention to the Matrix views, it doesn't care how the cells look.  if you want to change something in the Cells, you have to do it with Matrix methods. Check out both NSMatrix and NSCell. NSCell doesn't handle its own size, NSMatrix does.  

How do you get to the matrix in the browser so that you can configure it?

You can tell the browser to set its Matrix class to one you've developed, and configure the matrix class to automatically set its Cell Height in its init method.  Or you can override the Browser, add convenience methods for vertical Cell Sizing, and make all the necessary changes to it so that it does all the accounting with its various Matrices. 

----

How would one go about adding multiple unique cell types and sizes?  

It seems that the setCellClass and setCellSize methods of the NSMatrix class don't correctly override the super class implementations.  So far, I've only seen this work by manually adding columns to the NSBrowser based on user selection changes.

How do I create a preview column similar to that of Finder?

Subclass NSCell and override the drawInteriorWithFrame:inView: to draw your custom preview cell.  You also need to implement the NSBrowserCell methods isLeaf and isLoaded.  Now you use the target/action for the NSBrowser to setup a selectionDidChange callback. In this callback, check to see if the selected cell is a leaf and if so, manually add a column to the NSBrowser.  Now create your custom cell and add it to the NSMatrix for the newly created row.  I also check the that the column doesn't already exist to avoid recreating cells.

