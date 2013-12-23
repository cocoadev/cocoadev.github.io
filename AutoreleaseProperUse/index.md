---
layout: page
title: AutoreleaseProperUse
---

I've got an array in my class, and a method to resort this array, but this array is non-mutable, so I call     sortedArrayUsingFunction:context: and replace the non-sorted array with the new sorted array. However after finally getting it to work after several crashes, I am wondering if the memory management is correct, or proper (by the time this method is called,     _viewedTracks should already have a retain count of 1):
    _viewedTracks = _viewedTracks autorelease] sortedArrayUsingFunction:kSort context:@"title"];
[_viewedTracks retain];
I was previously using this and am still slightly puzzled why it wasn't working (it always crashed) - shouldn't the retain count stay the same??
    
_viewedTracks = [[[_viewedTracks copy] sortedArrayUsingFunction:kSort context:@"title"] release];

Any thoughts?

*The [[NSArray sort methods all work on non-mutable arrays, as opposed to the NSMutableArray     sortBy... methods. Therefore, like any other accessor-type method, they return autoreleased instances of a new array (basically calling     copy on it's own).     _viewedTracks is then set to this new array, so the old array is lost. The     autorelease ensures that the old array isn't left "orphaned" in the application's memory. Finally, the last     retain ensures that the sorted array is not deallocated. Basically, the first method ends up like this:*
    
original _viewedTracks: retainCount of 0
sorted version of _viewedTracks: retainCount of 1

*whereas your code ends up like this:*
    
original _viewedTracks: retainCount of 1
copy of _viewedTracks: retainCount of 1
sorted version of _viewedTracks: retainCount of -1

*Which, of course, has problems. Good luck! --JediKnil*

