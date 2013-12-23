---
layout: page
title: ForceRedrawOfTableView
---

Having some trouble with NSTableView.  It won't display the scrollbar properly, even though there are enough rows for it to be needed.  It shows the scrollbar as if there aren't enough rows for one to be necessary.  Here's the code I've used for a program which will display the fonts in a tableview.

    

#import "Controller.h"

@implementation Controller

-(void)awakeFromNib
{
    fonts = [[[NSFontManager sharedFontManager]availableFonts]retain];
    theFonts = [[NSMutableArray arrayWithArray:fonts]retain];
    [fonts release];
}

- (IBAction)refresh:(id)sender
{
    
}

- (int)numberOfRowsInTableView:(NSTableView *)aTableView
{
    return [theFonts count];
    [tableView setNeedsDisplay:YES];
}

- (id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
    return [theFonts objectAtIndex:rowIndex];
    [tableView setNeedsDisplay:YES];
}
@end



Did I do something wrong?  Thanks.

----

Well, one thing is that your -setNeedsDisplay: calls are never getting called because the function exits when you call return.  --Bo

----

Ah, thanks, but even calling setNeedsDisplay: doesn't seem to help... I can't figure out what's wrong.  Thanks for any help someone can provide.

----

Are you seeing anything in your table view?  If you aren't seeing anything in your table view it's because you haven't done one or some of the following: connect the table view to the appropriate outlet, have set the initalized outlet (in awakeFromNib) to have the controller as its data source... also, do a quick NSLog(@"number of rows = %i", [theFonts count) to see if you're source really has as many items as you think.

----

Yes, I'm certain there are the proper number of items in the TableView.  I can tell because I can press down arrow key, and go beyond the bottom of it (no scrolling though, just going beyond it, to an invisable part, due to lack of scolling), and have the delegate method go... your NSLog test worked too.  It's the proper number, for sure.

----

Quick question: If you add a [tableView reloadData]; at the end of your -awakeFromNib method, does the problem go away? -- Bo

----

Another thing; you might want to check you nib, double click the table view in your nib (if that's how the table view is getting there) pull up the info panel and double check that table view is checked for scrolling!

----

The bigger picture is that -setNeedsDisplay: doesn't work properly for NSTableView because a table view in InterfaceBuilder is actually a table view in a clip view in a scroll view, and you need to update all 3 together.  So, you really need to use -reloadData or -noteNumberOfRowsChanged when you want to redraw instead. -- Bo

----

Yep bo, that did it.  Thanks.  I needed to do reloadData.

see also NSTableViewTutorial

