---
layout: page
title: CanClassObjectsBePassedToMethods
---

I'm not really sure what to call this title.  This might be a retain count problem I'm dealing with.  I'll try to describe the problem.  Here is a section of code that calls the method "findLongestRoad:throughIntersection:" twice.  I pass a NSMutableArray object both times.  (POSITIVE and NEGATIVE are just two integer #define tags and aren't relevant)

    

	path = [[NSMutableArray alloc] init];
	[r findLongestRoad:path throughIntersection:POSITIVE];
	[path removeObject:r];
	[r findLongestRoad:path throughIntersection:NEGATIVE];
	totalRoads = [path count];
	[path release];



The implementation of method "findLongestRoad" is below.  At issue is confusion on my part as to what is happening with the loop at the end of the method.

    

- (void)findLongestRoad:(NSMutableArray *)path throughIntersection:(int)i
{
	int				loop;
	NSMutableArray  *paths[3];
	Intersection	*section;
	NSPoint			pathPoint;
	NSPoint			roadPoint1, roadPoint2;
	int				direction;

	if ([path containsObject:self])
		return;
	else
		[path insertObject:self atIndex:0];

	if (i > 0)
		section = positive;
	else
		section = negative;

	for (loop = 0; loop < 3; loop++)
		paths[loop] = [NSMutableArray arrayWithArray:path];

	if ((section roadLeft] player] == player) && ([section roadLeft] != self))
		[[section roadLeft] findLongestRoad:paths[0] throughIntersection:NEGATIVE];
	if (([[section roadRight] player] == player) && ([section roadRight] != self))
		[[section roadRight] findLongestRoad:paths[1] throughIntersection:POSITIVE];
	if (([[section roadVertical] player] == player) && ([section roadVertical] != self))
	{
		pathPoint = [section position];
		roadPoint1 = [[section roadVertical] positionFrom];
		roadPoint2 = [[section roadVertical] positionTo];
		if ((pathPoint.y < roadPoint1.y) || (pathPoint.y < roadPoint2.y))
			direction = NEGATIVE;
		else
			direction = POSITIVE;
		[[section roadVertical] findLongestRoad:paths[2] throughIntersection:direction];
	}

	for (loop = 0; loop < 3; loop++)
	{
		if ([paths[loop] count] > [path count])
		{
			[path release];
			path = [[[NSMutableArray arrayWithArray:paths[loop]];
			[path retain];
		}
	}
}



When initially allocated in the debugger, path is 0x514940.  The first time I call the method, path will have 2 objects, and when the "path = ...arrayWithArray" is executed, path keeps the address of 0x514940, thereby passing back to the main function the 2 object array.  The second time I call the method, we need to return a 3 object array.  The "path = ...arrayWithArray" on the second call for some reason returns a 3 object array but a different address of 0x5b7260.  This would be fine, except when it returns back to the main function, the path there still retains the old address of 0x514940 and the old 2 object array.

Can someone explain why it performs differently on two different calls?

-- JohnathanSteere
----
The pointer parameter *path* is passed by value and thus treated like any other local variable, the scope of the reassignment is local to the method. A solution to the problem would be to pass a pointer to the array (      NSMutableArray **path  ), but considering that it looks like it is coming in pre-assigned that might not be a good idea and it would not be a good design decision for a method to release something it does not own. Another solution would be to empty the passed array and re-populate the existing object. A third approach would just return the result array (as a return value) in the case where no reassignment was needed, i.e. retain/autorelease the passed array, otherwise just return a newly created auto-released array.

Regards

