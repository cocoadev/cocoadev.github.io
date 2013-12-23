---
layout: page
title: NSOutlineViewChildSelectionHelp
---

In my program that I'm working on, I have an outline view that has an endless number of folders and items in it.  My problem comes when a child of a folder is selected...  I need to be able to find out what child has been selected, and I don't know how.  I've been messing with this for a few months now and just recently found this:

    
- (BOOL)selectionShouldChangeInOutlineView:(NSOutlineView *)outlineView
{
    NSArray *selectedNodes = [self selectedNodes];
    NSData *data = [textField RTFDFromRange:NSMakeRange(0, textField string] length])];
    [[NSMutableDictionary *d = [NSMutableDictionary dictionaryWithDictionary:[selectedNodes objectAtIndex:0]];
    [d setObject:data forKey:@"Notes"];
    NSLog(@"...selectionShouldChangeInOutlineView");
}


That's all great, I can finally find out what the old item in an OutlineView was before changing, but I can't do anything with it because I have no idea what the child was that was selected.  See the third line of code:

    
    NSMutableDictionary *d = [NSMutableDictionary dictionaryWithDictionary:[selectedNodes objectAtIndex:0]];


At the very end is a zero.  From what I can tell, it's supposed to be a variable that represents the selected child inside a parent node.  Maybe I'm misunderstanding this, but what I'm trying to do isn't working.  I'm thinking it's just this.  I'm hoping someone has an answer or can point me in the right direction.  Apple's DragAndDropOutlineView does what I need, but it is very confusing.  The lack of commenting it makes it pretty hard to read.

Thanks in advance,
 - Daniel Howard | ideaSpiral | DVD Rack

----

I'm new to Cocoa so not sure if I understand correctly your question, but for the same sort of problem I've been using this

    

- (id)selectedItem {
 return [anOutlineView itemAtRow: [anOutlineView selectedRow]]; }



where anOutlineView is the identifier of the IBOutlet to your NSOutlineView.  This returns the currently selected item. The following returns a multiple selection:

    

- (NSArray*)allSelectedItems {
    NSMutableArray *items = [NSMutableArray array];
    NSEnumerator *selectedRows = [anOutlineView selectedRowEnumerator];
    NSNumber *selRow = nil;
    while( (selRow = [selectedRows nextObject]) ) {
        if ([anOutlineView itemAtRow:[selRow intValue]]) 
            [items addObject: [listOfSpectra itemAtRow:[selRow intValue]]];
    }
    return items;
}



I got these from a tutorial somewhere, or maybe from an Apple example- not sure where, so can't cite the source.

Ronald Cox

----

Aha, I figured it out!  I was simply doing a few things that weren't required.  After talking to one of my friends (who doesn't know Cocoa/Objective-C at all) he gave me a few ideas.  I sat down and after about 10 minutes of messing with it, I had it compeltely figured out.  I just needed to use the following:

-(id)itemForRow:(int)

Once I used that, everything became clear.

Cheers,
 - Daniel

