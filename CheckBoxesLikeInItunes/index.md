---
layout: page
title: CheckBoxesLikeInItunes
---

I have a column of check boxes in my NSTableView. when you click on one of these check boxes its tablerow gets selected and the check box is toggled.

but this is not the behavior i am after ... i need them to work like the check boxes in iTunes:
you click on the check box to toggle it, and its row is not selected at the same time. and when you click its title the tablerow is selected but the check box stays in its current state.

how is this possible ?

thanks a lot,
jan
----
Not really an answer to your question, but I will point out that iTunes is a Carbon application. I am looking at the same requirement that you outlined, I will shout if I come across anything.

----

I have thought with this myself, seen others fight it, and the Cocoa applications from Apple (Xcode, Mail (not checkmarks, but fold/unfold buttons for threads), etc.) do *not* work like iTunes -- so it is not likely that there is an easy solution.

I would suggest everyone who wants this behavior to file an enhancement report with apple.

--AllanOdgaard

----

Not very robust but I only spent a minute, and only that much to get you started...
    
@implementation CustomTableView

- (void)mouseDown:(NSEvent *)event
{
    NSPoint loc = [event locationInWindow];
    
    loc = [self convertPoint:loc fromView:nil];
    
    int row = [self rowAtPoint:loc];
    int col = [self columnAtPoint:loc];
    
    NSArray *cols = [self tableColumns];
    id column = [cols objectAtIndex:col];
    id cell = [column dataCellForRow:row];
    
    if ([cell isKindOfClass:[NSButtonCell class]]) {
        if (loc.x < 18) { // clicked in the checkbox..
           id dSource = [self dataSource];
            id curState = [dSource tableView:self objectValueForTableColumn:column row:row];

            if (dSource && [dSource respondsToSelector:@selector(tableView:setObjectValue:forTableColumn:row:)])
                [dSource tableView:self setObjectValue:(curState == kCFBooleanTrue ? kCFBooleanFalse : kCFBooleanTrue) forTableColumn:column row:row];
            return;
        } else { // clicked on the label portion of the checkbox..
            [self selectRow:row byExtendingSelection:NO];
            return;
        }
    }
    
    [super mouseDown:event];
}
@end


----

There has to be a better solution, because iSync does this. Under the iPod setup, you can select calendars and the checkboxes get checked but the row isn't highlighted, at least the way we're used to visually. Is there a way to override the table view's highlighting behavior, easily?

----

The EDTableView class here ( http://www.mulle-kybernetik.com/software/EDFrameworks/Documentation/EDCommon/Classes/EDTableView.html ) seems to be specialized on this behaviour.

