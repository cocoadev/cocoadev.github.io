---
layout: page
title: CocoaSTL
---

 

http://www.top-house.dk/~aae0030/cocoastl/

CocoaSTL is a header file with functions and classes to help use STL algorithms with Cocoa and Objective-C++. It contains amongst other:


*iterators to traverse NSArray, NSData, NSDictionary, NSIndexSet, NSSet and NSString,
*output iterators for NSArray, NSData, NSIndexSet, NSSet and NSString,
*smart pointer to handle retain/release for a wrapped ObjectiveC object, these also implement     operator< and friends by sending     isEqual: and     compare: to the wrapped object,
*function adaptor for selectors,
*neat foreach macro,
*helper functions to create iterators for any sequence in the same way (STL, C string, C array, Foundation class, ...).


Some examples:
    
// sort a primitive array of strings
objc_ptr<> strings[] = { @"This", @"Is", @"A", @"Test" };
std::sort(beginof(strings), endof(strings));

// shuffle an array
NSMutableArray* a = ...;
std::random_shuffle(beginof(a), endof(a));

// print all indices in an index-set
NSIndexSet* indices = ...;
foreach(it, beginof(indices), endof(indices))
   printf("\t%d\n", *it);

// convert an NSSet (with NSNumbers) to an NSIndexSet (of integers!)
NSSet* set = ...;
NSIndexSet* indices = [NSIndexSet indexSet];
std::transform(beginof(set), endof(set), back_inserter(indices),
   method<int>("intValue"));

// convert an NSIndexSet into an NSArray (of NSNumbers)
id make_number (int i) { return [NSNumber numberWithInt:i]; }

NSIndexSet* indices = ...;
NSMutableArray* array = [NSMutableArray array];
std::transform(beginof(indices), endof(indices), back_inserter(array),
   make_number);

// instead of make_number, one could create a functor in-place,
// but I figured it would scare non-STL fans to see code like this:
   std::bind1st(
      unary_method<int, id>("numberWithInt:")
      [NSNumber class]
   );

// what happens here is that unary_method returns a 'functor',
// i.e. what looks like a function with the prototype:
//    id functor (id self, int argument);
// and when called, it will do:
//    return [self numberWithInt:argument];
// the advantage of this transformation is that a) we can now
// pass it to all stl algorithms and b) we can use it with functions
// like bind1st and bind2nd, which bind one of the arguments.
// So if we have:
//    f = unary_method<int, id>("numberWithInt:");
// then we can now do:
//    g = bind1st(f, [NSNumber class]);
// g is now a new functor with the prototype:
//    id functor (int argument);
// because the first argument has permanently been bound to
// [NSNumber class]. So calling g(32) is now the same as:
//    return [[NSNumber class] numberWithInt:32];


----
The link to the CocoaSTL homepage seems to be dead, unfortunately.  Does anyone have a new link for it?  -- AndrePang.
----
me too want cocoaSTL. does any one has it?
----
Oh, that's so disappointing. I read through those examples thinking, yes! And then�no download :(

----

Until there's some official re-release or whatever, there's also archive.org:

http://web.archive.org/web/*/http://www.top-house.dk/~aae0030/cocoastl/

and more specifically:

http://web.archive.org/web/*/www.top-house.dk/~aae0030/cocoastl/CocoaSTL.h

I can't find the download page. Does anyone have a new link for it? Please help me.

