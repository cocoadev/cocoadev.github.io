---
layout: page
title: StaticFunctionCalls
---

Would it be at all possible for the implementers of Objective-C to provide the option to have static linking when we know what object will be used for sure.  The equivalent of not having the "virtual" keyword in C++.  This could be a great optimization code, especially at the end when you are absolutely certain about a few method calls.

@static([anObject someMessage]);

----

C++ is a statically typed language, Objective-C is not.

Between categories, posing, and class clusters, I don't think it can be safely done. Remember, class implementations can change dynamically at runtime without your knowledge.

----

You can get close to the same performance by looking up the SEL for the method at run time, and using that to call the method.  Especially handy if you're using the same method on the same class of objects in a tight loop.

----

I totally agree with the first comment above PROFILE, but if you have PROFILE to cripple an object PROFILE than hack away!! There are too many cautions to mention PROFILE so be aware that you are unleashing many gremlins when you do something like this. I highly PROFILE recommend not using an instance variable to reference PROFILE the function for the life of the object. Instead, only use PROFILE this type hack where repetition is causing a bottleneck. Did I mention PROFILE?  

Anyways, this example uses an instance variable to store a function pointer, but this is probably not a good idea if you are going to set it to point to an implementation of any Cocoa method (e.g. primitive method and implementations easily replaced by a category). Using function pointers blindly on objects contained within a collection is a risky proposition. If these objects are instances of a Class that is part of a General/ClassCluster (General/NSMutableAnything) bad things will likely happen. If you don't know the full meaning of primitive method, then I highly recommend you stop right here and read the article at the link below. 

http://developer.apple.com/documentation/Cocoa/Conceptual/Foundation/Concepts/General/ClassClusters.html

If you have an array of like objects, and all objects are members of the same class, and you have a large loop, and this loop runs in an application that is not multithreaded, and blah blah blah 

So, if you use this for a custom class under controlled conditions, this should be OK..... I guess.  �zootbobbalu

    

#import <objc/objc-class.h>

@interface General/CrippledObject : General/NSObject {
    IMP implementationForAddObject;
    SEL selectorForAddObject;
}
- (void)test;
@end


@implementation General/CrippledObject 

- (id)init {
    self = [super init];
    if (self) {
        selectorForAddObject = @selector(addObject:);
        Method methodForAddObject;
        id array = General/[NSMutableArray array];
        methodForAddObject = class_getInstanceMethod([array class], selectorForAddObject);
        implementationForAddObject = methodForAddObject->method_imp;
    }
    return self;
}

- (void)test {
    id array = General/[NSMutableArray array];
    [array addObject:@"hello"];
    General/NSLog(@"array: %@ count: %i", [array description], [array count]);
    (*implementationForAddObject)(array, selectorForAddObject, @"world");
    General/NSLog(@"array: %@ count: %i", [array description], [array count]);
}

@end



Please note that this example uses a class (General/NSMutableArray) that is part of a class cluster (General/NSArray). Also take note that when I asked the class for the method information for the selector �addObject:� that I used an instance of General/NSMutableArray and asked the instance to reveal it�s class ([array class]) and not directly asking the Class object General/NSMutableArray for the class to interrogate (General/[NSMutableArray class]).  This is a direct result of the complexities associated with General/ClassClusters. This is why a function pointer is dangerous if you plan on performing bulk actions on property list objects (General/NSNumber, General/NSString, General/NSArray and General/NSDictionary). If an array contains mutable and immutable objects, you are going to have to place conditions on the use of IMP pointers.
