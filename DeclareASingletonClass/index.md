---
layout: page
title: DeclareASingletonClass
---

To make your class always return a single instance of itself, use this init routine.

See also SingletonDesignPattern, SingletonAlternatives, 
    


SINGLETON CLASSES

- (id) init {
    static YourClass *sharedInstance = nil;

    if (sharedInstance) {
        [self autorelease];
        self = [sharedInstance retain];
    } else {
        self = [super init];
        if (self) {
            sharedInstance = [self retain];

            // initialize
        }
    }

    return self;
}
 

