---
layout: page
title: OriginalClass
---

**OriginalClass** -part of AspectCocoa

Here we define an example Class as a reference.. as an example of a Class that we might want to apply some Aspects too.  Other pages related to AspectCocoa will make reference to this Class.
----

    
#import <Foundation/Foundation.h>


@interface OriginalClass : NSObject {

}

- (id)a;

- (void)b;

- (void)c;

- (void)d:(NSString *)string;

- (id)e:(NSString *)string;

- (id)f:(NSString *)a f:(NSString *)b;

- (id)g:(id *)thing;


@end


----

    
#import "OriginalClass.h"

@implementation OriginalClass


- (id)a
{
    NSLog(@"a");
    return self;
}

- (void)b
{
    NSLog(@"b");
}

- (void)c
{
    NSLog(@"c");
}

- (void)d:(NSString *)string
{
    NSLog(@"%@ d:", string);
}

- (id)e:(NSString *)string
{
    NSLog(@"%@ e:", string);
    return self;
}

- (id)f:(NSString *)a f:(NSString *)b
{
    NSLog(@"%@ %@ f:f:", a, b);
    return self;
}

- (id)g:(id *)thing
{
    NSLog(@"%@ g:", thing);
    return self;
}

@end



**Shouldn't -g:'s parameter be (id) rather than (id *)? Otherwise it's a C-array of objects... and you're using it like it's a single object. So either change (id *) to (id) or change thing in the NSLog statement to *thing.**

