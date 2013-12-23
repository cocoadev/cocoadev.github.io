---
layout: page
title: ObjectCreation
---



**
*The later discussion covers this, but just to be clear:  Anything you obtain via an alloc, init, copy, or new method you are responsible for releasing.  Anything else is not your responsibility, and if you want to hold onto something then you should retain it.*
**

According to the Apple manual it takes two steps to create an object using Objective-C.  First you must dynamically allocate memory for the new object.  Second you must initialize the newly allocated memory to appropriate values. (see The Objective-C Programming Language for Mac OS X version 10.3, page 95, Apple Computer.)

Example
    
id anObject = [[SomeClass alloc] init];


But the following examples show the apparent creation of objects without using allocation and initialization.  (see COCOA Programming For Mac OS X, Second Edition, Aaron Hillegass.)

Examples

page 54
    
id z = @"123";


page 95
    
NSString *string = [textfield stringValue];


page 99
    
NSColor *initialColor = [textField textColor];


When are new objects required go through formal allocation and initialization and when not?

*Everything gets initialized, it's just a question of who does it and when.  When you say [textField textColor] you are returned a color object that someone else allocated and/or initialized (and thus you are not responsible for cleaning it up).*

----
But this still appears to be a very valid question. How many other "special cases" are there, and do we need to check the docs for every object to find out which ones may "allocate an object on our behalf"? And if it is possible to automatically initialize and allocate an object, why is this not done by default? What would be the conditions under which you want to create a new object but not allocate and initialize it?

*
To be clear, though this discussion is old, there are no special cases.  There are several your hands on an object, whether it's the return of a method or an @ directive or whatever.  How these objects came into being is not any of your business, the only question is whether you take possession of the object, which means that it's your responsibility to release it.  And here there is a uniform convention: you own the return from any method starting with     alloc,     copy, or     new, and you own any object that you     retain.  The _only_ caveat has to do with     init, which may replace one object owned by the caller with a new object owned by caller.  If your code always does     alloc and     init in one line, i.e.     NSMutableString *string = [[NSMutableString alloc] init];, then you do not have to think about the subtlety.
* 

----
The main thing is, you don't really need to know. If the method you call isn't a copy an alloc or retain then you don't have to care about releasing the returned object. It could be an existing object, it could be autoreleased object, it could a crazy object on which release does nothing (eg [NSColor blueColor). @"" is unique as far as I know.

----
The thing about the @"" strings is that they're constant. A string created with @"string" is that same immutable instance everywhere. It will always be around throughout the rest of your program's lifespan, and never autoreleased nor released. As far as I know, there's no comparable situation anywhere else in Cocoa-- although it might be nice to have arrays and the like with a similar syntax. -- RobRix
----
The rule that helped me fully understand when to alloc and when not to is this: You do not need to allocate memory for objects created with convience methods, such as     stringWithString:.  But if you are using methods such as     initWithString to create new objects you must allocate memory manually using the     alloc method, and of course if you use convience methods, use the     retain method if you want to use the object elsewhere.

