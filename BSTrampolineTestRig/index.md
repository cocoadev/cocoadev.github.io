---
layout: page
title: BSTrampolineTestRig
---



    
#import <Foundation/Foundation.h>
#import "NSArray(HigherOrderMessaging).h"

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    NSMutableString *a = [NSMutableString stringWithString:@"a"], *b = [NSMutableString stringWithString:@"b"], *c = [NSMutableString stringWithString:@"c"];
    NSArray *array = [[NSArray alloc] initWithObjects:a, b, c, nil];
    NSString *index;


    NSEnumerator *enumerator = [array objectEnumerator];
    while (index = [enumerator nextObject]) {
        NSLog(index);
    }


    NSLog(@"Testing: -do");
    array do] appendString:@"_"];

    enumerator = [array objectEnumerator];
    while (index = [enumerator nextObject]) {
        [[NSLog(index);
    }


    NSLog(@"Testing: -collect");
    NSArray *anotherArray = array collect] stringByAppendingString:@"_"];
    
    enumerator = [anotherArray objectEnumerator];
    while (index = [enumerator nextObject]) {
        [[NSLog(index);
    }


    NSLog(@"Testing: -select");
    anotherArray = array select] hasPrefix:@"a"];
    
    enumerator = [anotherArray objectEnumerator];
    while (index = [enumerator nextObject]) {
        [[NSLog(index);
    }


    NSLog(@"Testing: -reject");
    anotherArray = array reject] hasPrefix:@"a"];

    enumerator = [anotherArray objectEnumerator];
    while (index = [enumerator nextObject]) {
        [[NSLog(index);
    }

    [array release];
    [pool release];
    return 0;
}

