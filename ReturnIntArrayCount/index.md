---
layout: page
title: ReturnIntArrayCount
---

Hi There...

Im having some issues with returning a integer through a method.

Heres my code:
- ( NSNumber *) getTotalExpressions
{
	NSArray  *expressionsSplit = myExpression stringValue] componentsSeparatedByString:@"%"];
	
	return (int)[ expressionsSplit count ];
}

I keep on getting the error:
warning: return makes pointer from integer without a cast

If I call the method and log the retuned value, it always results in 0, however, if I log the count within the method, I recieve the correct value...

Im sure im doing something wrong, but I don't know cocoa well enough to work out what....

Any help would be greatly appreciated !!!

----

Your method is declared to return an [[NSNumber *. You're returning an int. These two are not the same, and there is no automatic conversion between them.

----
NSNumber is a class, int is a primitive type.

*You are promising to return an object that's an instance of the NSNumber class by returning a pointer to it (that's what the * stands for). However, you are returning a number (of whatever size int is on your machine) rather than a pointer. You can fix the method in one of two ways:*


*Promise an int rather than a number, with     - (int) getTotalExpressions (rather than - (NSNumber*) ...).
*Actually return a pointer to a NSNumber instance by creating one with     [NSNumber numberWithInt:...], for instance     return [NSNumber numberWithInt:[expressionSplits count]];.

