---
layout: page
title: UndoActionNameInconsistency
---

Let's say you have a list of items (perhaps displayed in an General/NSTableView) with methods to add and remove an item. You want to add undo/redo support so you implement the following in your controller class.

    
- (void)addItem:(Item *)item
{
	[undoManager registerUndoWithTarget:self selector:@selector(removeItem:) object:item];
	[undoManager setActionName:@"Add Item"];
	
	[items addObject:item];
	
	// Update the UI
}

- (void)removeItem:(Item *)item
{
	[undoManager registerUndoWithTarget:self selector:@selector(addItem:) object:item];
	[undoManager setActionName:@"Remove Item"];
	
	[items removeObject:item];
	
	// Update the UI
}


Pretty basic, however, this introduces a problem with the naming of the redo action. For example, if you add an item, then choose "Undo Add Item", the redo menu will then read "Redo Remove Item" because the name of the action was changed upon removing of the item (it should read "Redo Add Item" instead). There are several ways to get around this problem, but I'm wondering how all of you out there do it.

-- General/RyanBates

What I is to only set the name when I'm not undoing or redoing (e.g. the first time around) using isUndoing and isRedoing.
