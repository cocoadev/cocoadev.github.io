---
layout: page
title: CustomSegmentedControls
---



Hi, I'm trying to make a custom looking NSSegmentedControl but just can't get it to work...
If I just want the buttons to have another look, do I need to subclass NSSegmentedControl or NSSegmentedCell?

I can't understand how they work together :(

Tried subclassing NSSegmentedCell and used my own - (void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView
but when I called segmentControl cell] setCellClass:myOwnCellClass] the program chrashes...
I called it in awakeFromNib just as it says in the reference...

Anyone who have done this and could help me?

----

Read the Control And Cell Programming Topics for Cocoa document: http://developer.apple.com/documentation/Cocoa/Conceptual/[[ControlCell/index.html

You'll need to customize the *cell* since it is the cell that is responsible for drawing.

----

Ok, thanks for the link, but Istill can't get this stuff to work :(
I've subclassed NSSegmentedControl:
    
@implementation RoundedSegmentedControl

+ (Class) cellClass
{
	return [RoundedSegmentedCell class];
}
@end


and NSSegmentedCell:
    
@implementation RoundedSegmentedCell

- (void)drawSegment:(int)segment inFrame:(NSRect)frame withView:(NSView *)controlView
{
    NSImage *img = [NSImage imageNamed:@"DummyImage.png"];	
    
    [img drawInRect: [self bounds]  
		   fromRect: NSZeroRect 
		  operation: NSCompositeSourceOver 
		   fraction: 1.0]; 
    [img release];
}


In IB I've set my NSSegmentedControl to my RoundedSegmentedControl

I get a call to RoundedSegmentedCell: +initialize but when I check myRoundedSegmentControl cell] class] I get [[NSSegmentedCell anyway...
So, I don't understand why my Cell-subclass gets called and the is ignored anyway...

----
You have to do more work to get your custom cell to be used for an IB-created control. This post has more details on why and how to work around it: http://www.mikeash.com/blog/pivot/entry.php?id=17

