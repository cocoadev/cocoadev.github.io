---
layout: page
title: NSOutlineViewDraggingRows
---



How do I implement dragging in an NSOutlineView? More specifically, if I know what rows (and/or parents) are selected how do I drop them without screwing up the indexes?

I'm using something like this right now to: 

- see what rows are highlighted

- see if the parent is highlighted (thus making all the children highlighted as well)

- if the child is alone (parent not highlighted), make it a parent

- else put the parent in the array and the children should follow (I think)

- store the new parents and children in _draggedObjects

- and remember the old indexes with _oldRows

(btw, this is only for a one level hierarchy at the moment though feel free to explain in full)

    

- (BOOL)outlineView:(NSOutlineView *)oView writeRows:(NSArray *)rows toPasteboard:(NSPasteboard *)pboard
{
    [self setDragNodeArrayWithRows:rows];
    
    [pboard declareTypes: [NSArray arrayWithObjects: QuestionDragType, nil] owner: self];
    
    _oldRows = rows;
}

- (void)dragNodeArrayWithRows:(NSArray *)rows
{
    int i;
    
    Node *node;
    Node *parent;
    
    for (i = [rows count]; i > 0; i--) {
        
        node = [outlineView itemAtRow:rows objectAtIndex: i] intValue;
        
        if (parent = [node parent]) { // check to see if we're dealing with a root
            
            if ([outlineView isRowSelected:[outlineView rowForItem: parent]]) { // check to see if the parent is highlighted
                [_draggedObjects addObject: parent]; // if so, just add the parent
               
            }
        } else {
            [node setParent:nil]; // make it a parent
            [_draggedObjects addObject: node]; // else, if the child is alone, just add it
        }
    }
}


 I guess this is more of a combination question rather... just: Is what I'm doing now correct, and how can I manage the method:

- (BOOL)outlineView:(NSOutlineView *)oView
       acceptDrop:(id <NSDraggingInfo>)info
              row:(int)row
    dropOperation:(NSTableViewDropOperation)op

- JohnDevor

----

Given:
    
NSArray* draggedItems = ...;
NSArray* insertionArray = ...; // destination array
int insertAtIndex = ...; // where the user dropped the items

All we need to do is to adjust insertAtIndex to correspond to the index after removing the draggedItems -- that is, every item in draggedItems which is also before insertAtIndex, should move insertAtIndex one back.

This can be expressed like this:
    
NSMutableSet* dragSet = [NSMutableSet setWithArray:draggedItems];
NSArray* before = [insertionArray subarrayWithRange:NSMakeRange(0, insertAtIndex)];
[dragSet intersectSet:[NSSet setWithArray:before]];
insertAtIndex -= [dragSet count];


Alternatively one could use node-coloring (tagging), similar to what is discussed in NSOutlineViewRemoveItem.

----

Alright, I'll try it later. Thanks.

----

Anyone using this page to get started on implementing this should look out - the above code contains an incorrectly named method. It should be:

    
- (BOOL)outlineView:(NSOutlineView *) oView writeItems:(NSArray *) rows toPasteboard:(NSPasteboard *) pboard



I fell foul of this since the Xcode documentation at first appears to lack these delegate methods and so I cut and pasted the code above as a starting point. The Apple sample code is however correct. The info is in the Xcode docs - just not listed under the delegate methods for NSOutlineView - instead they can be found at  NSOutlineViewDataSource protocol --GrahamCox

