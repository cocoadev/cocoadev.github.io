---
layout: page
title: NSRange
---

[http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Functions/FoundationFunctions.html]
or, more worky-like: [http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Miscellaneous/Foundation_DataTypes/Reference/reference.html] and [http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Miscellaneous/Foundation_Functions/Reference/reference.html]

NSRange is pretty useful when it comes to describing an interval. It's used to say, "from X to, but not including, X+Y". If you want such a range, then just type     NSMakeRange (X, Y).

Invalid ranges are usually specified with:
    NSMakeRange (NSNotFound, 0)

Check for this with:
    if (range.location == NSNotFound) �

----

NSMaxRange(range) returns the next location after the range passed to it.

----

I have always been curious to why there is no NSZeroRange defined (like NSZeroPoint, NSZeroRect, NSZeroSize), then when writing this, I looked in the documentation, and surely it says:

*ZeroRange: An NSRange set to 0 in location and length*

But it's not in any of the headers...

----

Well, not that it explains much, but I only see a reference to ZeroRange in the Cocoa Java documentation.  Is it possible it's only available in Java?

----

Apparently so. At least, it never worked in my entire minute of testing in an ObjectiveCee project.

----

It's dead easy to add: 

    
#define NSZeroRange NSMakeRange(0,0)


Or you could *avoid* setting yourself up for problems with getting into Apple's namespace by using a variable of your own...

    
// in the header
extern const NSRange MyZeroRange;

// in the implementation file
NSRange MyZeroRange = {0, 0};


----

Or, you could use the magic of preprocessors using the following:

    
#ifndef NSZeroRange
#define NSZeroRange NSMakeRange(0,0)
#endif


You know, just in case they ever fix their oversight in the future.

----

The latter example wouldn't actually protect you, since Apple favours defining such things as extern const values rather than as macros. So #ifndef NSZeroRange will always evaluate to true.

