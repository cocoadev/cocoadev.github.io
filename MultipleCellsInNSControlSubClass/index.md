---
layout: page
title: MultipleCellsInNSControlSubClass
---


Hello, I am trying to subclass NSControl so I can layout multiple NSCells. I know most people would just say to use NSMatrix or NSTableView, but I would like more control over the NSCells, instead of having them be all the same size, same spacing, etc. if I used NSMatrix. Does anybody know what is involved in subclassing NSControl for use with multiple NSCells? I could really use your help on this. Thanks in advance.
- Luke


----
This gave me a lot of trouble back when I first got started. The trick is that you have to keep track of everything yourself. You are still writing a     NSView subclass--    NSControl doesn't add as much as it looks like.

Start with a consistent way of addressing your cells. A table or matrix might use {row, column} (but if you need a table or matrix, you might want to look at     NSTableView or     NSMatrix). Then write a couple of methods that let you do basic things like get the frame of any cell. You may need to cache the frames (or aspects of them, like row height) if you have thousands of cells. If column width is fast but row height is slow, don't cache both, because then if the width changes you'll have to recache the height too.

The interface between     NSControl and     NSCell is mostly abstract, and you can usually do your own thing if you want to. Methods like     -[NSControl drawCell:] smell--if the cell knew where it should draw, it'd be an NSView. Instead, use something like     -drawCellAtRow:column: or     -drawCell:withFrame:. Then     -drawRect: can just loop over the rows and columns that need display.

Off the top of my head, here are some important     NSCell methods:
    
- (void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView;
+ (BOOL)prefersTrackingUntilMouseUp;
- (void)highlight:(BOOL)flag withFrame:(NSRect)cellFrame inView:(NSView *)controlView;
- (BOOL)trackMouse:(NSEvent *)theEvent inRect:(NSRect)cellFrame ofView:(NSView *)controlView untilMouseUp:(BOOL)flag;
- (void)resetCursorRect:(NSRect)cellFrame inView:(NSView *)controlView;

// If you want your cells to be editable:
- (void)editWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject event:(NSEvent *)theEvent;
- (void)selectWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject start:(int)selStart length:(int)selLength;
- (void)endEditing:(NSText *)textObj;


The methods     -highlight:... and     -trackMouse:... should
880
 be called directly from     -mouseDown: in a loop with     -[NSApplication nextEventMatchingMask:untilDate:inMode:dequeue:]. When the mouse is enters the cell, you send     -highlight:YES..., then     -trackMouse:..., and when     -trackMouse:... returns, it will tell you whether the mouse went up or whether it's still down and outside of the cell. If the latter, send     -highlight:NO... and keep looping. MouseTracking probably deserves a page all its own.

----
If the cells are going to be in spreadsheet formation, then subclassing NSMatrix and implementing your own     drawRect: method is the best way to start. This way you already have an interface to access/modify/add/delete cells based on a row column design.

----
That's true, but there is a dearth of information on subclassing     NSControl.

----
theres a dearth of information about EVERY class. the trick is to follow the inheritance. NSControl is a View. learn about views, then you know that much more about NSControls.
but thats just general stuff. 
I've actually developed arbitrary Cell Based NSControls. the thing you have to understand is that there is NO magic. NSControl keeps an instance of NSCell, and passes calls to it to handle certain functions. there is really good documentation for the communication between NSCell and NSControl, and in there you can learn really easily what you need to override.  All you need to do is , create an array of Cells they can even be of different classes, or multiple arrays of different classes of cells. and then you Have to override the methods in your Control to pass the right calls to the right cells. this includes (but is not exclusive to) drawing, mousing, and actions.  One thing to remember, Cells by design don't know anything about their Coordinate system. when they are told to draw, they are selfish about it. They don't need to lock focus, or anything, and they always draw relative to their parent View (the control). 

Theres no magic there, its all really simple and designed to be overridden.

