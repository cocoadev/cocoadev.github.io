---
layout: page
title: NSDictionaryAndNSOutlineViewBugs
---

After many many frustrating hours trying to figure out why my outline view would crash, when my own queries of the data source revealed exactly what i expected to find, I finally found a solution, but exactly what goes on is beyond my understanding.

I use the following code to set up an outline view with two items which each have one child. All items are NSDictionaries with the keys "CHILDREN" (an optional NSArray of children), "LABEL" (the name of the item) and "PARENT" (not really used, but a pointer to the parent item). http://goo.gl/OeSCu

    
- (void)awakeFromNib
{
   id item1 = [self getDefaultNewItem];
   id item2 = [self getDefaultNewItem];
   id group1 = [self getDefaultNewGroup];
   id group2 = [self getDefaultNewGroup];

   group1 objectForKey:@"CHILDREN"] addObject:item1];
   [item1 setObject:group1 forKey:@"PARENT"];   // CAUSES A PROBLEM!!!
   [[group2 objectForKey:@"CHILDREN"] addObject:item2];
   [item2 setObject:group2 forKey:@"PARENT"];   // CAUSES A PROBLEM!!!

   [[GroupsAndFilesArray = [[NSMutableArray alloc] init];
   [GroupsAndFilesArray addObject:group1];
   [GroupsAndFilesArray addObject:group2];

   [OutlineView reloadData];
}


If I start my app then it show the two groups. I can open one, and it works, I can close it again and then open the other, and it still works -- but what if I open both? bang... my application will crash... the debugger points to some deeply nested Cocoa code, and there is no trace of my own code.

So how did I solve it? well, either by changing the key PARENT to e.g. "It sucks that the source for Cocoa is not public", that will make it work (but not all strings will make it work).

Another solution is to change the object given for the PARENT key. But if I use e.g. item1 and item2 or the two CHILDREN arrays from group1 and group2, then it still crashes. But other objects seem to work.

A third possibility is not to use NSDictionary as the items, but a simple wrapper for NSDictionary.

So wtf is going on here??? I am really clueless to why my app should crash and really would like to know if there are some fundamental things I have missed.

----

Can you paste in the stack trace?  Someone might be able to recognize the "deeply nested Cocoa code".  Also what do getDefaultNewItem  and getDefaultNewGroup do?  Maybe include that code too.

----

    
- (NSMutableDictionary *)getDefaultNewItem
{
   static unsigned cnt = 0;
   NSMutableDictionary *dict = [NSMutableDictionary dictionary];
   [dict setObject:[NSString stringWithFormat:@"untitled %d", ++cnt] forKey:@"LABEL"];
   return dict;
}

- (NSMutableDictionary *)getDefaultNewGroup
{
   static unsigned cnt = 0;
   NSMutableDictionary *dict = [NSMutableDictionary dictionary];
   [dict setObject:[NSString stringWithFormat:@"Group %d", ++cnt] forKey:@"LABEL"];
   [dict setObject:[NSMutableArray array] forKey:@"CHILDREN"];
   return dict;
}


And the stack-trace:

    
#0	0x901a3ad4 in __CFCopyRegularEthernetAddrString
#1	0x90147464 in CFDataCreate
#2	0x901369d4 in CFEqual
#3	0x901474a8 in CFDataCreate
#4	0x901369d4 in CFEqual
#5	0x90147960 in CFDictionaryGetValueIfPresent
#6	0x901369d4 in CFEqual
#7	0x901474a8 in CFDataCreate
#8	0x901369d4 in CFEqual
#9	0x901474a8 in CFDataCreate
#10	0x901369d4 in CFEqual
#11	0x90147960 in CFDictionaryGetValueIfPresent
#12	0x901369d4 in CFEqual
#13	0x901474a8 in CFDataCreate
#14	0x901369d4 in CFEqual
#15	0x901474a8 in CFDataCreate
#16	0x901369d4 in CFEqual
#17	0x90147960 in CFDictionaryGetValueIfPresent
#18	0x901369d4 in CFEqual
#19	0x901474a8 in CFDataCreate
#20	0x901369d4 in CFEqual
#21	0x901474a8 in CFDataCreate
#22	0x901369d4 in CFEqual
#23	0x90147960 in CFDictionaryGetValueIfPresent
#24	0x901369d4 in CFEqual
#25	0x901474a8 in CFDataCreate
#26	0x901369d4 in CFEqual
#27	0x901474a8 in CFDataCreate
#28	0x901369d4 in CFEqual
#29	0x90147960 in CFDictionaryGetValueIfPresent
#30	0x901369d4 in CFEqual
...
#5111	0x90147960 in CFDictionaryGetValueIfPresent
#5112	0x901369d4 in CFEqual
#5113	0x901474a8 in CFDataCreate
#5114	0x901369d4 in CFEqual
#5115	0x901474a8 in CFDataCreate
#5116	0x901369d4 in CFEqual
#5117	0x90147960 in CFDictionaryGetValueIfPresent
#5118	0x901369d4 in CFEqual
#5119	0x901474a8 in CFDataCreate
#5120	0x901369d4 in CFEqual
#5121	0x901a9cc4 in __CFSetAddValueAndReturn
#5122	0x90140c4c in CFSetGetValueIfPresent
#5123	0x97e09e40 in -[NSCFSet member:]
#5124	0x93140498 in -[NSOutlineView _expandItemEntry:expandChildren:]
#5125	0x93208fb0 in -[NSOutlineView expandItem:expandChildren:]
#5126	0x932d6b84 in -[NSOutlineView mouseTracker:didStopTrackingWithEvent:]
#5127	0x932036a4 in -[NSMouseTracker stopTrackingWithEvent:]
#5128	0x93202d74 in -[NSMouseTracker trackWithEvent:inView:withDelegate:]
#5129	0x931b11a4 in -[NSOutlineView mouseDown:]
#5130	0x930c102c in -[NSWindow sendEvent:]
#5131	0x930a8e20 in -[NSApplication sendEvent:]
#5132	0x930b1dac in -[NSApplication run]
#5133	0x9315fc58 in NSApplicationMain
#5134	0x00003da4 in ??
#5135	0x00003c24 in ??


So it would seem that NSOutlineView somehow picks up my PARENT key, reads the object and finds out it is a dictionary and continues infinitely... during this process *none* of my data source members are invoked -- infact, from the time of clicking the item (to expand) and to the crash, nothing is requested from my code.

I just got the chance to test my code on two other systems, one being a Panther pre-release, where the problem is gone, the other was Jaguar, which also crashed -- weather it has actually been fixed for Panther is uncertain, as the problem did come up in several strange context (and the release notes said nothing of the sort, but then again, according to the release notes very few bugs have been fixed, and this would seriously be terrible -- Apple focusing mainly on features and neglecting to fix the hundreds of bugs in their OS).

----

Sounds like an item is self referencing.

----

Well, it refers to its parent -- but this is a key *I* have defined (and which I do not use). Why would the system decide to follow my keys??? Why would it even think that the object I return is an NSDictionary? And even more strange, why does it only do it when *two* of my items are expanded (and not one)?


----

How much different does this method implementation look from yours?

    
-(id)outlineView:(id)outlineView child:(int)index ofItem:(id)item {
    id children=[item objectForKey:@"CHILDREN"];
    id child=[children objectAtIndex:index];
    id parent=[child objectForKey:@"PARENT"];
    NSLog(@"child: %@ parent: %@", child, [child objectForKey:@"PARENT"]);  
    return child;
}


and, if you added the log statement, can you report what your app does?

----

For the sake of completeness, here is the full data source implementation:

    
- (int)outlineView:(NSOutlineView *)outlineView numberOfChildrenOfItem:(id)item
{
   NSArray *array = item ? [item objectForKey:@"CHILDREN"] : GroupsAndFilesArray;
   return array ? [array count] : 0;
}

- (BOOL)outlineView:(NSOutlineView *)outlineView isItemExpandable:(id)item
{
   return [item objectForKey:@"CHILDREN"] != nil;
}

- (id)outlineView:(NSOutlineView *)outlineView child:(int)index ofItem:(id)item
{
   NSArray *array = item ? [item objectForKey:@"CHILDREN"] : GroupsAndFilesArray;
   return [array objectAtIndex:index];
}

- (id)outlineView:(NSOutlineView *)outlineView objectValueForTableColumn:(NSTableColumn *)tableColumn byItem:(id)item
{
   return [item objectForKey:[tableColumn identifier]];
}


The NSLog won't work because I have cyclic data and thus description will loop forever. However, the crash happens after asking 'is item expandable' and *before* requesting the actual child.

I have also placed the entire project at: http://www.top-house.dk/~nr8/OutlineView.tgz (46.8 KB).

----

Right, I forgot to return root for item==nil. Everything looks ok AFAICT. Have you tried cleaning and rebuilding your project (I usually do this if things just don't make sense).

----

... is the array's retained or autoreleased ?
-- PtxMac

----

The arrays are autoreleased -- and yes, I did try to "clean", I even started and entirely new project and wrote the code from scratch.

And now I just discovered that my solution about wrapping the NSDictionaries in my own class still yields problems, not as often as before, but my app did just crash (re-produceable and with stack pointing to OS code) -- so I now did a special ivar for the parent point (no longer stores it in the dictionary) and it seems to work now.

Perhaps the problem is not really with NSOutlineView but instead with NSDictionary doing some sort of traversing of all objects (conforming to property list items) and their contained children on certain events... although I have never ever read anything about it, and am clueless to why it should do so and what would trigger it...

----

The problem is you are using a strong reference when you:

    
   [item1 setObject:group1 forKey:@"PARENT"];   // CAUSES A PROBLEM!!!



Your should use a weak reference object to refernce your parent object:

    
@interface WeakReference : NSObject {
    id parent;
}
+(id)weakReferenceWithParent:(id)parent;
-(void)setParent:(id)_parent;
-(id)parent;
@end
@implementation WeakReference
+(id)weakReferenceWithParent:(id)parent {
    id weakRef=[[[WeakReference alloc] init] autorelease];
    [weakRef setParent:parent];
    return weakRef;
}
-(void)setParent:(id)_parent {
    parent=_parent;
}
-(id)parent {
    return parent;
}

@end


So you should add your parent reference to your item like this: 

    
   [item1 setObject:[WeakReference weakReferenceWithParent:group1] forKey:@"PARENT"];   // CAUSES A PROBLEM!!!



The reason your app is looping is because you are creating a retain loop when you directly set group1 for key PARENT. --zootbobbalu

----

That would be the case if invoking retain on NSArray objects or NSDictionary objects would propagate to all children. But where is it said that they do? and this would be a terrible inefficient design if it was the case.

E.g. this code

    
   NSArray *last = [NSArray array];
   for(unsigned i = 0; i < 1000; i++)
      last = [NSArray arrayWithObject:last];


Would lead to around 500.000 retains being invoked (although 1.000 should suffer).

----

Taken straight from DragNDropOutlineView example that comes with development tools:

*TreeNode is a node in a doubly linked tree data structure.  TreeNode's have weak references to their parent (to avoid retain cycles since parents retain their children).*

Why this is with container objects is beyond me, but the method calls involved with displaying an outline view don't ask item nodes for their parent reference so using a weak reference object shouldn't be a performace issue. --zootbobbalu

----

Yes, a "retain cycle" will occur, but this is not the same as having the system go into an infinite loop to retain all objects. It just means that if you release the parent, it will not be disposed, because the children will also have retained it, so to release a parent you will need to tear loose all children first (to break the retain cycle).

If the design is that retaining an NSArray or NSDictionary should increase the retain count for each child, then a) major speedups would be easily possible throughout OS X and b) it would be impossible to represent any cyclic data using the system supplied arrays and dictionaries (and cyclic data do appear in real life! for this reason their serialisation system is even made to handle cycles in the serialised data).

So I really do not think that "having retain cycles" is "asking for the system to crash".

I don't know if you tried my example, but you will also find that it is not the actual retain or setObject which cause the crash, it is when *using* the items in the NSOutlineView.

----

I don't think it's a retain cycle issue.  If you use a different key than  @"PARENT", it works, which implies that PARENT is some magical value being used somewhere.  As mentioned above, if something like @"xxxPARENTxxx" is used, life is totaly groovy as far as the outlineview is, which makes me think the retain/weak linking thing isn't the real solution.

----

I posted a data source that works for me (NSOutlineVIew). Let me know if this works for you. --zootbobbalu

I tried to directly set group with @"xxxPARENT" and I get a retain cycle. I don't know what else to say. Maybe someone might be able to shed some light on this if this is a bug or a retain cycle issue.

----

The retain cycle will cause problems because your objects will not be freed properly, but it seems that the crash is happening because NSDictionary's -isEqual: method is recursive and breaks if you set up a loop among your dictionaries.  If you want to set up a tree structure with backward links, you may want to take a look at the CFBridging framework at http://tufty.co.uk/Software/objectware.html .  It contains a tree class that wraps CoreFoundation's CFTree.  Or you could just write your own and make sure your -isEqual: only checks your children and your label.  -- Bo

----

Actually, Apple says that after some investigation this is a known issue --     isEqual: should not break because     [obj isEqual:obj] should return     YES, even when there are cyclic references (since a pointer comparison will be done before any *deep* comparison), and comparing two self referencing dictionaries with     isEqual: does in fact work.

----
I think the problem is in comparing group1 to group2 (and then trying to compare the children).

It will enumerate the keys of the children and if the "PARENT" or "CHILDREN" key comes before the "LABEL" key, it can turn into an infinite recursion as it tries to compare group1 to group2 which compares child1 to child2 which compares group1 to group2, etc.

Changing the name of the PARENT key can change the enumeration order and make LABEL compare earlier which will terminate the recursion.
----

