---
layout: page
title: InsertingTextIntoNSString
---

How would I go about examining an NSString, and if it contains any spaces ' ', then insert a '+' sign, where the space was?

Example:

    
NSString *orignalString = @"I program in Cocoa.";

and end up like:

NSString *orignalString = @"I+program+in+Cocoa.";


Any help would be appreciated.


A quick peek at the NSMutableString documentation shows the method -replaceCharactersInRange:withString: and is just what you want. See how simple that was? Just read the docs bud... :-) --KevinPerry

----

For some reason I cant get it to work

----

The following code prints out "I+program+in+Cocoa." for me. --Bo
    
NSMutableString* myString = [@"I program in Cocoa." mutableCopy];

[myString replaceOccurrencesOfString:@" " withString:@"+" options:0 range:NSMakeRange(0, [myString length])];
NSLog(@"%@", myString);


----

Thanks man, works perfect.

