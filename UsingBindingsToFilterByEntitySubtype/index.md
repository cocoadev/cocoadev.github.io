---
layout: page
title: UsingBindingsToFilterByEntitySubtype
---



I have created a data model similar to the following:

    
Shape Group
[name | shape]
  |
  |
  Shape (abstract entity)
  [identifier]
 / \                  / \  
  |                    |
Square              Circle
[lowerLeft, height] [center, radius]


Shape Group is an entity with an attribute called name, and a 1-to-many relationship called shape, connected to the entity called Shape.
Shape is an abstract entity that has an attribute called identifier.
Square is a subtype of Shape, and has attributes lowerLeft and height.
Circle is another subtype of Shape, has attributes center and radius.

Using:
	[fetchRequest setEntity:General/[NSEntityDescription entityForName:@"Circle" inManagedObjectContext:objectContext]];
	[fetchRequest setPredicate: General/[NSPredicate predicateWithFormat:@"(identifier LIKE **')"]];
I can get all the Circle entities from the object context.  Getting the Squares is similar.

So far so good.

In Interface builder, I set up an interface similar to the following:

    
-- Shape Group --------------------
Shape Group 1
Shape Group 2
-----------------------------------

--------[*Square*| Circle ]---------
Square 1
Square 2
-----------------------------------


On top is a General/NSTableView for selecting one of several different shape groups.
At the bottom is a General/NSTabView that displays either all the squares in the currently-selected shape group (using another General/NSTableView), or all the circles of the currently-selected shape group (using a third General/NSTableView).

The problem occurs when I try to get only the shapes of the currently-selected shape group to appear.  For example, I went to the array controller controlling the squares, and tried to set contentSet to bind to the shapegroup.selection.shape model key path.  Unfortunately, that also picks up all the circle shapes, resulting in a valueForUndefinedKey exception (because circles do not have a lowerLeft or height attribute)

Is there any way I can modify the contentSet in Interface Builder so that it picks up only Squares or only Circles?

----

Um ... set the controller's entity to "Square" or "Circle" then tell it to -fetch: ? 

----

Thank you for your help.  I was kind of hoping to be able to do this with contentSets in Interface Builder alone, but I'll go with the working version :) 

    
...

General/[ShapeGroupController addObserver:self forKeyPath:@"selectionIndexes" options:General/NSKeyValueObservingOptionNew context:NULL];
General/[SquareController setEntityName:@"Square"];

...

- (void)observeValueForKeyPath:(General/NSString *)keyPath ofObject:(id)object change:(General/NSDictionary *)change context:(void *)context
{
    if ([keyPath isEqual:@"selectionIndexes"]) {
		General/NSArray *selectedObjects = [object selectedObjects];
		if ([selectedObjects count] > 0)
		{
			General/NSString *currentGroup = General/selectedObjects objectAtIndex:0] valueForKey:@"name"];  // assuming no multiple selection...
			[[[SquareController setFetchPredicate:General/[NSPredicate predicateWithFormat:@"shapeGroup.name LIKE %@", currentGroup]];
			General/[SquareController fetch:self];
		}
    }

    [super observeValueForKeyPath:keyPath ofObject:object change:change context:context];
}

