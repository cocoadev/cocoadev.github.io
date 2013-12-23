---
layout: page
title: NSArray
---

Documentation at the AppleDeveloperConnection:

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/index.html#//apple_ref/doc/uid/TP40003620

See FoundationCollections for an overview; see DesignArray for a critique of the array design in general

----


NSArray implements     -(BOOL)doesContain:(id)anObject which returns true if the array contains anObject, by identity.

----
Note that the same behavior can be gotten through documented methods with the following code:
    
- (BOOL)containsObjectIdenticalTo:(id)anObject
{
	return NSNotFound != [self indexOfObjectIdenticalTo:anObject];
}


And in fact I imagine this is how     -doesContain: is implemented.

----

Another excellent method that NSArray provides is     -(void) makeObjectsPerformSelector:(SEL)aSel
    
[myArray makeObjectsPerformSelector:@selector(stroke)];


----
Yeah, but if they were thinking clearly, they would have returned a new array containing the results of each method call.

----
Like this?
    - (NSArray *)makeObjectsPerformSelectorAndReturnResults:(SEL)selector
{
	NSMutableArray *results = [NSMutableArray array];
	NSEnumerator *e = [self objectEnumerator];
	id obj;
	while (obj = [e nextObject])
	{
		if ([obj respondsToSelector:selector])
			[results addObject:[obj performSelector:selector]];
	}
	return results;
}

----
Yeah, thanks! I didn't expect code...this could be useful.

----
There are several methods of this sort in cocoadev's NSArrayCategory.

----
I don't know if the code above should be considered reliable; there are lots of selectors where the methods don't return objects. Although I don't think there's any two method signatures with differing return types, there could be in the future. I've been working on a catch-all for this in another context. It's not done yet, but when it is I'll see about posting it.

----
Note that Cocoa already provides a similar method, via     valueForKey:. These two lines are basically equivalent:

    
newArray = [array makeObjectsPerformSelectorAndReturnResults: @selector( someMethod )];
newArray = [array valueForKey: @"someMethod"];


As a bonus, the built-in method will autobox return values from methods that return primitives and it will also grab ivars directly if you provide its name and there's no accessor method.

----

Check out the -mapUsingSelector: method of the NSArray+Functional category: http://seriot.ch/software_cli.php#nsarray_functional

It allows you to do the same but with variable arguments selectors.

    
NSArray *a = [NSArray arrayWithObjects:@"a", @"ab", @"abc", @"bc", @"c", nil];

NSArray *x = [a filterUsingSelector:@selector(hasPrefix:), @"a", nil];
NSArray *y = [a mapUsingSelector:@selector(uppercaseString), nil];
NSArray *z = [a reduceUsingSelector:@selector(stringByAppendingString:)];


Results:

    
x: (a, ab, abc)
y: (A, AB, ABC, BC, C)
z: aababcbcc


----

**Sample Code:**


*IteratingThroughAnArray
*SortUsingSelector
*TerminateExplicitListWithNil

