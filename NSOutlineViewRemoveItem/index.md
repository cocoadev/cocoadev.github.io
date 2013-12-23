---
layout: page
title: NSOutlineViewRemoveItem
---

In an NSOutlineView I want to be able to delete the currently selected item.  Often this item is buried in a few nested folders, so just deleting the selected row doesn't work.  I've tried a few different methods for deleting it, but none of them work.  The way my outlineView is set up, It's an array of dictionaries full of arrays, etc...  So to delete the selected note, I need to somehow find the dictionary that is containing the selected array.  If I knew how to do that, this would all be easy.  The code below doesn't work:

    
noteBoxView itemAtRow:[noteBoxView selectedRow removeAllObjects];


All this does is delete the actual dictionary.  Although there is no dictionary keys left inside of the array, the array is still understanding that it is there (because I haven't removed it from the array itself).  Does anyone know how to do this?  Any help would be greatly appreciated.

Thanks,
 - Daniel Howard
 
 ----
 
There is unfortunately no easy way. The naive solution will be to search your entire tree structure for the selected item (and keeping a note of its parent) -- alternatively you can let each item keep a pointer to its parent, which require some extra maintenance, and you cannot store the parent pointer in the dictionary (which you currently use for items), because that will introduce a cycle in the "tree", and as discussed earlier here, it seems the NSOutlineView will use the isEqual: selector to compare your items (to reduce redisplay when items have changed), and this selector will do an item-by-item isEqual in the dictionary, which will be recursive when seeing your parent item...

 ----

Rather than search in your datasource for the selected items (which would require a linear search for each item, i.e. removing m out of n items would require O(n x m) operations) then you can simply iterate all the selected items, and insert some special key into these items (like @"REMOVE ME!"), and afterwards, you iterate all the items of the datasource, removing items with this key (giving a running time of O(n + m), where m < n, so at worst 2n => O(n)).

Here is some code, assuming an array (named Items) of dictionaries, where the key Children contains an array of children, if the node is expandable:
    
- (void)removeSelectedItemsFromOutlineView:(NSOutlineView*)outlineView
{
   NSEnumerator* enumerator = [outlineView selectedRowEnumerator];
   while(NSNumber* row = [enumerator nextObject])
   {
      id item = [outlineView itemAtRow:[row intValue]];
      [item setObject:@"YES" forKey:@"Delete"];
   }
   [self removeObjectsWithKey:@"Delete" fromArray:Items];
   [outlineView deselectAll:self];
   [outlineView reloadData];
}

- (void)removeObjectsWithKey:(id)aKey fromArray:(NSMutableArray*)anArray
{
   for(unsigned i = [anArray count]; i != 0; )
   {
      id item = [anArray objectAtIndex:--i];
      if(NSMutableArray* children = [item objectForKey:@"Children"])
         [self removeObjectsWithKey:aKey fromArray:children];
      if([item objectForKey:aKey] != nil)
         [anArray removeObjectAtIndex:i];
   }
}


--AllanOdgaard

----

Why not just have each item reference their parent. It's really simple with NSValue. While you are adding a child to a node do the following:
(this example uses an NSMutableDictionary for each item) 

*Maybe because the code is of approximately same length, but saves you the maintenance overhead involved in re-arranging items, grouping/ungrouping or de-serialising items from disk -- btw: I think your code is flawed, since it will modify the tree while iterating the selected items, plus it may release a parent before releasing potentially selected children of this parent.* --AllanOdgaard

You're right about my flaw. I typed this example up without testing it first, but I do use weak references all the time. Having double links are handy when drag and drop comes into play, this is why I like using weak references IMHO. --zootbobbalu

*Double-linking is fine, though I try to avoid it because I consider it redundant info which must be kept up to date at all time, which is just added complexity -- with regard to drag and drop, I have pasted some code below which is the essence of the drag and drop I use for my outline views, it uses setKey:forItemsInArray:, which just loops over the items and sets the key to YES, and is in lack of a makeObjectsPerformSelector: which take two objects, I would imagine the parent-pointer version to be more complex, due to the degenerate cases.* --AllanOdgaard

The degenerate case where the parent pointer is a broken link is not possible if a single method is used to detach a node from the tree and a single method is used for adoption. I'm not aware of other degenerate cases that will corrupt this data structure, so please enlighten me of the other bug sources. I do approve of your attempts to implement an NSDictionary as an independent node object. This is also my ultimate goal!! I would love to do away with the weak reference for all the reasons you have pointed out (some pun intended), so if you have an elegant way to determine inheritance (path) to prevent dropping items on direct descendents, please share any ideas. --zootbobbalu

*We can get all the ancestors of an item with this function (written as an array category extension), the **expected** running time is worse than if we had parent pointers, but worst-case is the same, and it's linear, so probably not that big a deal to worry about:*
    
- (NSMutableArray*)pathForItem:(id)anItem
{
   NSEnumerator* enumerator = [self objectEnumerator];
   while(id item = [enumerator nextObject])
   {
      if(item == anItem)
         return [NSMutableArray arrayWithObject:item];
      if(NSMutableArray* path = item objectForKey:@"Children"] pathForItem:anItem])
         return [path addObject:item], path;
   }
   return nil;
}

*When we have all the ancestors, we just need to check if this set intersects the set of items being dragged, and in fact [[NSSet has a selector to compare two sets for intersection (and can be constructed from an array) -- I would expect the intersection selector to be linear in the number of items in the set, but construction from an array is probably O(n lg n), so if running time is important, we can instead setObject:forKey: on all the items being dragged, then check all the ancestors if any of the items have the key set, and remove the key again afterwards, which would be linear, but would require more code (and n here is only the number of items being dragged, or ancestors of the item to drop upon, whichever is biggest).* --AllanOdgaard

*P.S. Even though it may look like the function above expects each item to have an NSArray-object for the @"Children"-key, then it will work fine even when this object is absent, since sending a message to a nil-object is defined to return nil*

----

    

- (id)newItemForGroup:(id)group {
    id newItem = [NSMutableDictionary dictionary];
    id siblings;
    if (group) siblings = [group objectForKey:@"children"];
    else siblings = rootArray;
    if (group) [newItem setObject:[NSValue valueWithPointer: group] forKey:@"parentReference"];
    [siblings addObject:newItem];
    return newItem;
}



Then add these methods to remove the selected items from the tree;

    

- (void)removeItemFromTree:(id)item {
    id parentReference = [item objectForKey:@"parentReference"];
    id parent = (id)[parentReference pointerValue];
    if (parent) parent objectForKey:@"children"] removeObject:item];
    else [rootArray removeObject:item]; 
    [item removeObjectForKey:@"parentReference"];
}


- (void)removeSelectedItems {
    id row;
    id selectedRowEnumerator = [outlineView selectedRowEnumerator];
    id itemsToRemove = [[[NSMutableArray array];
    while (row = [selectedRowEnumerator nextObject]) {
        int rowIndex = [row intValue];
        id item = [outlineView itemAtRow:rowIndex];
        [itemsToRemove addObject:item];
    }
    id item;
    id itemEnumerator = [itemsToRemove objectEnumerator];
    while (item = [itemEnumerator nextObject]) {
        [self removeItemFromTree:item];
    }

}



--zootbobbalu

----
Function which moves all items from the array 'items' to the array 'children' at position 'index'. Will handle the degenerate case where both a parent and its child are moved (afterwards both will be at same level), and will also adjust 'index' in case items are removed in front of the insertion point. The variable 'Items' is the array containing the root level items for the 'tree'.
    
- (void)moveItems:(NSArray*)items toArray:(NSMutableArray*)children atIndex:(int)index
{
   [self setKey:@"Drag" forItemsInArray:items];
   for(int i = index; i != 0; )
   {
      id item = [children objectAtIndex:--i];
      if(item dictionary] objectForKey:@"Drag"])
         --index;
   }
   [self removeObjectsWithKey:@"Drag" fromArray:Items];
   [items makeObjectsPerformSelector:@selector(removeObjectForKey:) withObject:@"Drag"];
   [children insertObjectsFromArray:items atIndex:index];
}


----

I thank all of you for trying to help me, but I'm guessing that I don't grasp what any of you are saying.  None of the above code works in my project.  Of course I have modified it so that it 'works' in my project, it just never deletes the item.  The item just stays there.  No errors, or anything.  A few of the above code samples I couldn't even get to run.  I don't know what I'm doing wrong, but if any of you have some more advice or can give me a running example or something, please do that.  Thanks in advance.

Cheers,
 - Daniel


----

My code is written using [[ObjectiveCeePlusPlus, so either rename your source file extension to "mm" or rewrite it like this:
    
- (void)removeObjectsWithKey:(id)aKey fromArray:(NSMutableArray*)anArray
{
   NSMutableArray* children;
   unsigned i;

   for(i = [anArray count]; i != 0; )
   {
      id item = [anArray objectAtIndex:--i];
      if(children = [item objectForKey:@"Children"])
         [self removeObjectsWithKey:aKey fromArray:children];
      if([item objectForKey:aKey] != nil)
         [anArray removeObjectAtIndex:i];
   }
}

- (void)removeSelectedItemsFromOutlineView:(NSOutlineView*)outlineView
{
   NSNumber* row;

   NSEnumerator* enumerator = [outlineView selectedRowEnumerator];
   while(row = [enumerator nextObject])
   {
      id item = [outlineView itemAtRow:[row intValue]];
      [item setObject:@"YES" forKey:@"Delete"];
   }
   [self removeObjectsWithKey:@"Delete" fromArray:Items];
   [outlineView deselectAll:self];
   [outlineView reloadData];
}


The latter functions assume that you have an instance variable named 'Items' which is an array that contain all your items, and that your items are dictionaries with an optinal @"Children"-key holding an array with the children of that item.

If this doesn't work then please post some of your code! --AllanOdgaard

 ----

The above worked great, thanks!  -- Daniel

