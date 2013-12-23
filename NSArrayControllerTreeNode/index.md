---
layout: page
title: NSArrayControllerTreeNode
---

_NSArrayControllerTreeNode is often returned by [NSOutlineView itemAtRow:i]. There is no documentation on it. The header for it can be found at http://www.blueboxmoon.com/wiki/?page=Binding%20Tree

As always, be wary of undocumented APIs. Apple will feel no reservations about pulling them right out from under you.

    
@interface _NSArrayControllerTreeNode : NSObject

//
// Get the number of child nodes under this node.
//
- (int)count;

//
// Get the index path of this node in the tree.
//
- (NSIndexPath *)indexPath;

//
// Insert the given user object at the specified index number.
//
- (void)insertObject:(id)aObject atIndex:(int)nIndex;

//
// Insert the given user object at the specified index path, relative
// to this node.
//
- (void)insertObject:(id)aObject atIndexPath:(NSIndexPath *)indexPath;

//
// Returns YES if this node is a leaf (no children), NO if it has children.
//
- (BOOL)isLeaf;

//
// Get the child node at the given index path, relative to this node.
//
- (_NSArrayControllerTreeNode *)nodeAtIndexPath:(NSIndexPath *)indexPath;

//
// Get the user object at the given index path, relative to this node.
//
- (id)objectAtIndexPath:(NSIndexPath *)indexPath;

//
// Retrieve the user object for this node.
//
- (id)observedObject;

//
// Get the parent node of this node. If this is a top level object the
// returned object is of class _NSArrayControllerTreeNode, otherwise it
// is of class _NSArrayControllerTreeNode.
//
- (id)parentNode;

//
// Removes the child node at the given index.
//
- (int)removeObjectAtIndex:(int)nIndex;

//
// Remove the child node at the given index path, relative to this node.
//
- (int)removeObjectAtIndexPath:(NSIndexPath *)indexPath;

//
// Set the user object to be observed by this node. Probably a dangerous method
// to call when the tree is "active".
//
- (id)setObservedObject:(id)aObject;

//
// Change the parent of this node to another node. This moves the called
// node to another node in the tree. This method does not appear to fully
// work when called directly. It probably is meant to be used in combination
// (or via) the insertObject: methods.
//
- (id)setParentNode:(_NSArrayControllerTreeNode *)aNode;

//
// Set the sort descriptors for this node. This sorts only
// the children of this node and not the entire tree.
//
- (void)setSortDescriptors:(NSArray *)sortDescriptors;

//
// Retrieve the child node at the given index under this node.
//
- (_NSArrayControllerTreeNode *)subnodeAtIndex:(int)nIndex;

//
// These are completely unknown methods.
//
//- (void)prune; - Does not delete items.
//93b56f88 t -[_NSArrayControllerTreeNode observeValueForKeyPath:ofObject:change:context:]
//93b56da4 t -[_NSArrayControllerTreeNode allowsSubnodeAtIndex:]
//93b56e80 t -[_NSArrayControllerTreeNode startObservingModelKeyPath:]
//93b55dd8 t -[_NSArrayControllerTreeNode updateSubnodes]
// nm

@end


Include that header to get rid of warnings when using itemAtRow:

----
Do you have examples on how to use this class ? I would like to know how you initialize it with a "user object". It crashes when I do setObservedObject: on an instance created with alloc-init. Tx.

