---
layout: page
title: DraggingPboardHasTypeButNoObject
---

I am seriously pulling out my hairs on this one... I have an NSOutlineView where I want local drag'n'drop, and I have these methods in my datasource:
    
- (NSDragOperation)outlineView:(NSOutlineView*)outlineView validateDrop:(id <NSDraggingInfo>)info proposedItem:(id)item proposedChildIndex:(int)index
{
	return NSDragOperationMove;
}

- (BOOL)outlineView:(NSOutlineView*)outlineView writeItems:(NSArray*)items toPasteboard:(NSPasteboard*)pboard
{
	[pboard declareTypes:[NSArray arrayWithObjects:@"MyItemsPboardType", nil] owner:nil];
	[pboard setPropertyList:items copy] autorelease] forType:@"[[MyItemsPboardType"];
	return YES;
}

- (BOOL)outlineView:(NSOutlineView*)outlineView acceptDrop:(id <NSDraggingInfo>)info item:(id)anItem childIndex:(int)index
{
	NSPasteboard* pboard = [info draggingPasteboard];

	NSString* type = [pboard availableTypeFromArray:[NSArray arrayWithObjects:@"MyItemsPboardType", nil]];
	NSArray* items = [pboard propertyListForType:type];

	NSLog(@"Available type: %@", type);
	NSLog(@"Drag items: %@", items);
}


It does report the MyItemsPboardType when I drag and drop items, but the array is nil. Did I overlook something?

For the records, I also added this to my NSOutlineView subclass:
    
- (NSDragOperation)draggingSourceOperationMaskForLocal:(BOOL)isLocal
{
	return isLocal ? NSDragOperationMove : NSDragOperationCopy;
}


**Ahh, figured it out! The setPropertyList: takes a property list, which is an array containing property list items, i.e. not my own items, these are stripped... frustrating...**

Is there any easy way to store custom items on the pasteboard? It would seem I am forced to serialise my items into an NSData or similar...

And after fiddling with NSArchiver, I realise this is not what i want -- I want the pointers to stay the same, serialising and unserialising results in a copy of the original items, I need the original items so that they can be removed from the outline view... arg... I see that the DragNDrop example just uses a instance variable to store the items during the drag... so much for the drag pasteboard to carry the data for local drags...

----

You shouldn't really drag pointers to your items during a drag. Dragged items can be dropped in other applications, or can be dropped on the desktop, where they'll be saved as a clipping file. If you put a pointer in your drag, someone may create a clipping file, quit your application (releasing the pointer implicitly), restart your application and drop the now stale pointer back into your app. *bang*

That said, you could just cast your pointer to a long and store it in an NSNumber, and attach that to your drag as a property list. You might want to put it into a dictionary, with a second entry that contains a timestamp that indicates when the drag started, and an NSData representation of your object. When you get a drop, check the timestamp against that of the most recent drag. If they don't match, it's likely a clipping file, so just create a new item from the NSData. Otherwise you can use the pointer.

NB - Just saw you're doing a local drag. I guess in that case it's fine to just stuff your pointer in an NSNumber.  -- UliKusterer

