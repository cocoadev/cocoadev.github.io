---
layout: page
title: ColorWellsInTableViews
---

Hello, how would I go about putting a ColorWell in a TableView column? Do I need to subclass NSActionCell somehow? Thanks allot for any help you can provide!

----

Yes I guess you would have to subclass NSActionCell and create your own color well cell since NSColorWell is one of the few NSControl classes that does not use a cell for it's internal workings. 

It would be nice if someone could post a tutorial (or a link to one) on creating your own controls with a cell counterpart (ideally complete with a IB-palette). I just get confused by all the methods in the relevant classes that I don't really know where to begin.I can make it work but don't really know how to make it work the way it should work... -Gabbe

Well, tell us how you've done it and we'll comment and eventually refactor down to a tutorial. It's the spirit of Wiki! -- KritTer

----

Here is how I have done this:
    

@interface MyColorCell : NSCell
@end

@implementation MyColorCell

// Draw the rectangle:
- (void)drawInteriorWithFrame:(NSRect)inCellFrame
                       inView:(NSView *)inControlView
{
    NSGraphicsContext *currentContext
            = [NSGraphicsContext currentContext];
    [(NSColor*)[self representedObject] set];
    --inCellFrame.size.height;
    ::CGContextFillRect((CGContextRef)[currentContext graphicsPort],
                        *((CGRect*) &inCellFrame));
}

@end

- (void)awakeFromNib
{
    // Set the color column:
    MyColorCell *colorCell = [[MyColorCell allocWithZone:NULL]
                                  initImageCell:nil];
    mTableView tableColumnWithIdentifier:kColorColumnIdentifier_]
            setDataCell:colorCell];
    [colorCell release];
}

// Nice and simple. Neo

Yeah, I guess we could now comment and refactor :)

Although staring at it, I'm impressed by its author. How **does** it work?

-- [[KritTer

Whoa. Where's the cell's represented object getting set? I assume that must be done externally... I figure if you're creating a new cell anyhow, you might as well make the colour be an ivar and leave -representedObject in ClientSpace like it's supposed to be.

-- RobRix

I have a basic implementation of a ColorWellCell here: http://github.com/lakshmivyas/lvcolorwellcell

--Lakshmi Vyas

