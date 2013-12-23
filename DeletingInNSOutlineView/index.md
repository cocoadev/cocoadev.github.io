---
layout: page
title: DeletingInNSOutlineView
---



I'm using an NSOutlineView in my program.  I am new to using NSOutlineView, so maybe there's something I missed, but I don't think so.  I want to be able to do deletion in the NSOutlineView.  My NSWindowController subclass is set up as the delegate and data source of the NSOutlineView.  I cannot for the life of me figure out how to do the basic things: cut, copy, paste, and deleting items.  How do you do it? -RossDude
----
Under the Model-View-Controller paradigm:

*the View (in this case, an NSOutlineView) displays information, and collects the user's instructions on how to manipulate it;
*the Model stores this information (I'm supposing that you're using some sort of NSArray or NSDictionary to hold data); and
*the Controller layer translates input from the View into changes to the Model (your NSWindowController subclass is the Controller layer).

So the simple answer to your question is to add the relevant methods (actions) to your NSWindowController. If you're only interested in cutting, copying, pasting and deleting, you don't even need to wire these up to anything in Interface Builder: they're part of the standard responder chain. 

The following snippet demonstrates how you might implement these methods. There's plenty of stuff already here on CocoaDev about DragAndDrop, which is a similar topic...

--Tom

    

//
// in your subclass of NSWindowController
//
		
-(IBAction)copy:(id)sender{
	NSPasteboard* pboard=[NSPasteboard generalPasteboard];
	[pboard declareTypes:[NSArray arrayWithObject:myPboardType] owner:self];
	[pboard setData:[NSArchiver archivedDataWithRootObject:[outlineView allSelectedItems]] forType:myPboardType];
	}

-(IBAction)cut:(id)sender{
	NSPasteboard* pboard=[NSPasteboard generalPasteboard];
	[pboard declareTypes:[NSArray arrayWithObject:myPboardType] owner:self];
	[pboard setData:[NSArchiver archivedDataWithRootObject:[outlineView allSelectedItems]] forType:myPboardType];
	[self delete:sender];
	[outlineView selectRow:[outlineView selectedRow] byExtendingSelection:NO];
	[outlineView reloadData];
	}

-(IBAction)delete:(id)sender{
	NSEnumerator* enumerator=outlineView allSelectedItems] objectEnumerator];
	id candidate;
	while(candidate=[enumerator nextObject])
		[dataArray removeObject:candidate];
	[outlineView selectRow:[outlineView selectedRow] byExtendingSelection:NO];
	[outlineView reloadData];
	}
	
-([[IBAction)paste:(id)sender{
	NSPasteboard* pboard=[NSPasteboard generalPasteboard];
	if(pboard types] containsObject:myPboardType]){
		[[NSEnumerator* enumerator=[[NSUnarchiver unarchiveObjectWithData:[pboard dataForType:myPboardType]] objectEnumerator];
		id object;
		while(object=[enumerator nextObject])
			[dataArray insertObject:object atIndex:0];
		[outlineView selectRow:[contents selectedRow] byExtendingSelection:NO];
		[outlineView reloadData];
		}
	}

//
// in your subclass of NSOutlineView
//

-(NSArray*)allSelectedItems{
	NSMutableArray* items=[NSMutableArray array];
	NSEnumerator* enumerator=[self selectedRowEnumerator];
	NSNumber *row=nil;
	while(row=[enumerator nextObject])
		[items addObject:[self itemAtRow:[row intValue]]];
	return items;
	}



Alright, that looks like it will work, but how do I get the delete: method to trigger when the delete key goes down?

I've been musing on this one for a while myself. It's not too easy to have Interface Builder create a menu item which recognises the delete key... I suppose you might try:

    

//
// in your subclass of NSOutlineView
//

-(void)keyDown(NSEvent*)theEvent{
	if([theEvent keyCode] & NSDeleteFunctionKey)
		[self doCommandBySelector:@selector(delete:)];
	else
		[super keyDown:theEvent];
	}



--Tom

Okay, I get that.  Everything looks okay (I have gotten it into my program), but I don't understand the paste method.  The paste method seems odd to me.  If I'm looking at it correctly, it looks like you want me to make the thing paste at the very end of the outline view all the time.  I want to do something similar to OmniOutliner's behavior.  It seems to me that Apple should open up some of the private instance variables, since it is hard to get the parent of an item and vice versa from the NSOutlineView subclass.

