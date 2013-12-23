---
layout: page
title: NSOutlineViewDataSource
---



http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/Reference/Reference.html

http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/OutlineView/Articles/UsingOutlineDataSource.html

Protocol Description

Outline views support a data source delegate in addition to the regular delegate object. The data source delegate provides data and information about that data to the outline view. The regular delegate object handles all other delegate responsibilities for the outline view.

----

Here's a quick and dirty implementation of a data source for an outline view: 

     

#import "Controller.h"
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

@implementation Controller
-(NSMutableDictionary *)groupWithTitle:(id)title {
    NSMutableDictionary *group=[NSMutableDictionary dictionary];
    [group setObject:[NSMutableArray array] forKey:@"CHILDREN"];
    if (title) [group setObject:title forKey:@"TITLE"];
    return group;
}
-(NSMutableDictionary *)newItemForGroup:(NSMutableDictionary *)group title:(id)title{
    NSMutableDictionary *item=[NSMutableDictionary dictionary];
    id children=[group objectForKey:@"CHILDREN"];
    [children addObject:item];
    if (title) [item setObject:title forKey:@"TITLE"];
    if (group) [item setObject:[WeakReference weakReferenceWithParent:group] forKey:@"PARENT"];
    return item;
}
-(void)awakeFromNib {
    NSLog(@"awakeFromNib");
    dataStore=[[NSMutableArray array]  retain];
    id group1=[self groupWithTitle:@"group1"];
    id group2=[self groupWithTitle:@"group2"];
    [self newItemForGroup:group1 title:@"item1"];
    [self newItemForGroup:group2 title:@"item2"];
    
    [dataStore addObject:group1];
    [dataStore addObject:group2];
    [OutlineView reloadData];
}


-(BOOL)outlineView:(NSOutlineView *)ov isItemExpandable:(id)item {
    id children;
    if (!item) {
        children=dataStore;
    } else {
        children=[item objectForKey:@"CHILDREN"];
    }
    if ((!children) || ([children count]<1)) return NO;
    return YES;
}
-(int)outlineView:(NSOutlineView *)ov numberOfChildrenOfItem:(id)item {
    id children;
    if (!item) {
        children=dataStore;
    } else {
        children=[item objectForKey:@"CHILDREN"];
    }
    return [children count];
}
-(id)outlineView:(NSOutlineView *)ov child:(int)index ofItem:(id)item {
    id children;
    if (!item) {
        children=dataStore;
    } else {
        children=[item objectForKey:@"CHILDREN"];
    }
    if ((!children) || ([children count]<=index)) return nil;
    return [children objectAtIndex:index];
}
-(id)outlineView:(NSOutlineView *)ov objectValueForTableColumn:(NSTableColumn *)tableColumn byItem:(id)item {
    return [item objectForKey:[tableColumn identifier]];
}

@end



You have to use a weak reference object to reference your parent object when using an NSDictionary as an item node or else you are going to create a retain cycle if you directly set a group node object as the parent object in an item dictionary. 

*it is not a retain-cycle, the problem is that the outline view will compare items (to minimise redisplay) and the compare method of NSDictionary does a member-wise compare, so if your dictionary contains an indirect reference to itself, it will loop forever* --AllanOdgaard

You know that makes more sense. I only thought that because file:///Developer/Examples/AppKit/DragNDropOutlineView/ReadMe.rtf says:

*"TreeNode is a node in a doubly linked tree data structure.  TreeNode's have weak references to their parent (to avoid retain cycles, anyway the parents retain their children)."*  --Apple

Someone else here was having problems with an outline view (NSDictionaryAndNSOutlineViewBugs) and Bo thought that the -isEqual: method was the culprit:

Bo wrote: * "The retain cycle will cause problems because your objects will not be freed properly, but it seems that the crash is happening because NSDictionary's -isEqual: method is recursive and breaks if you set up a loop among your dictionaries.  If you want to set up a tree structure with backward links, you may want to take a look at the CFBridging? framework at http://tufty.co.uk/Software/objectware.html .  It contains a tree class that wraps CoreFoundation's CFTree?.  Or you could just write your own and make sure your -isEqual: only checks your children and your label." * 
--zootbobbalu

do this:

    
    [item1 setObject:[WeakReference weakReferenceWithParent:group1] forKey:@"PARENT"];


not this: 

    
    [item1 setObject:group1 forKey:@"PARENT"];


--zootbobbalu

