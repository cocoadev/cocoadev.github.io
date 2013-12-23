---
layout: page
title: NSImageToolTip
---

Is it possible to assign a tooltip to an NSImage? I can't seem to figure this out. It doesn't look like there's any easy way to do it. I see that NSView has a toolTip method, but I need an image in a tableView cell to have a toolTip, so I don't want to create a custom view for this.

Any ideas? This is literally the last thing in my application that isn't done. --GarrettMurray

----

You can assign a tooltip for the column in the tableview by creating a tooltip rectangle. Something along the lines of:
    
colTag = [self addToolTipRect:[self rectOfColumn:i] owner:self userData:nil];


Where i is the column that you wish (grab it using the identifier. -- MatPeterson

----

You might add a tooltip rect that's the same size as your table. Then your tooltip owner could implement something like this...
    
// Conform to the NSToolTipOwner protocol..
- (NSString *)view:(NSView *)view stringForToolTip:(NSToolTipTag)tag point:(NSPoint)point userData:(void *)userData
{   // get the row and column.. 
    int row = [yourTable rowAtPoint:point];
    int col = [yourTable columnAtPoint:point];
     
     // farm out the "thinking" to another method?..
     return [self tooltipForRow:row column:col];
}


This way you could display a tooltip in any column and row. HTH

