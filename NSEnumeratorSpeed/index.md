---
layout: page
title: NSEnumeratorSpeed
---

I got tired of the endless (very small) arguments over whether it's faster to use NSEnumerator or a for loop to enumerate an NSArray, so I thought I'd make a test and answer the question once and for all. http://goo.gl/OeSCu
    
/*
    enumerator_speed_test.m
    
    Enumeration speed tester
    
    This program tests the speed of NSEnumerator, compared with
    using a for loop to enumerate over the NSArray.
    
    Compile with this command:
    cc -framework CoreFoundation -framework Foundation -std=c99 -o enumerator_speed_test enumerator_speed_test.m
*/

#import <Foundation/Foundation.h>
#import <CoreFoundation/CoreFoundation.h>
#include <time.h>

void PrintUsage(char *name)
{
    fprintf(stderr, "usage: %s <array_size> <iterations>\n", name);
    exit(1);
}

int main(int argc, char **argv)
{
    id outerPool = [NSAutoreleasePool new];
    id tempPool;
    clock_t startTime, endTime;
    
    if(argc != 3)
        PrintUsage(argv[0]);
    
    int arraySize = atoi(argv[1]);
    int iterations = atoi(argv[2]);
    
    // create a large array we can enumerate over
    tempPool = [NSAutoreleasePool new];
    NSMutableArray *mutArray = [NSMutableArray array];
    for(int i = 0; i < arraySize; i++)
        [mutArray addObject:@"test!"];
    NSArray *array = [mutArray copy]; // create immutable copy
    [tempPool release];
    
    // test NSEnumerator speed
    fprintf(stderr, "Enumerating through array with %d elements, %d iterations, using NSEnumerator...", arraySize, iterations);
    startTime = clock();
    for(int i = 0; i < iterations; i++)
    {
        tempPool = [NSAutoreleasePool new];
        NSEnumerator *enumerator = [array objectEnumerator];
        id obj;
        while((obj = [enumerator nextObject]))
            ; // do nothing
        [tempPool release];
    }
    endTime = clock();
    fprintf(stderr, "done.\nTotal time taken: %f sec\n\n", (float)(endTime - startTime)/CLOCKS_PER_SEC);
    
    // test for loop speed
    fprintf(stderr, "Enumerating through array with %d elements, %d iterations, using for loop...", arraySize, iterations);
    startTime = clock();
    for(int i = 0; i < iterations; i++)
    {
        tempPool = [NSAutoreleasePool new];
        unsigned arrayCount = [array count];
        for(unsigned j = 0; j < arrayCount; j++)
        {
            id obj;
            obj = [array objectAtIndex:j];
        }
        [tempPool release];
    }
    endTime = clock();
    fprintf(stderr, "done.\nTotal time taken: %f sec\n\n", (float)(endTime - startTime)/CLOCKS_PER_SEC);
    
fprintf(stderr, "Enumerating through array with %d elements, %d iterations, using CFArray for loop...", arraySize, iterations);
    startTime = clock();
    for(int i = 0; i < iterations; ++i) {
                const unsigned arrayCount = CFArrayGetCount((CFArrayRef)array);
        for(unsigned j = 0; j < arrayCount; ++j)
        {
            id obj = (id) CFArrayGetValueAtIndex((CFArrayRef) array, j);
        }
    }
    endTime = clock();
    fprintf(stderr, "done.\nTotal time taken: %f sec\n\n", (float)(endTime - startTime)/CLOCKS_PER_SEC);

    fprintf(stderr, "Enumerating through array with %d elements, %d iterations, using objectAtIndex: function pointer for loop...", arraySize, iterations);
    startTime = clock();
    for(int i = 0; i < iterations; ++i) {
        SEL selector = @selector(objectAtIndex:);
        id (*pFn)(id, SEL, unsigned);
        pFn = (id (*)(id, SEL, unsigned)) [array methodForSelector:selector];
                const unsigned arrayCount = [array count];
        tempPool = [NSAutoreleasePool new];
        for(unsigned j = 0; j < arrayCount; ++j)
        {
            id obj = (id) pFn(array, selector, j);
        }
        [tempPool release];
    }
    endTime = clock();
    fprintf(stderr, "done.\nTotal time taken: %f sec\n\n", (float)(endTime - startTime)/CLOCKS_PER_SEC);
    
    fprintf(stderr, "Enumeratinng through array with %d elements, %d iterations, using nextObject function pointer and NSEnumerator...", arraySize, iterations);
    startTime = clock();
    for(int i = 0; i < iterations; i++)
    {
        tempPool = [NSAutoreleasePool new];
        NSEnumerator *enumerator = [array objectEnumerator];
        SEL selector = @selector(nextObject);
        id (*pFn)(id, SEL);
        pFn = (id (*)(id, SEL))[enumerator methodForSelector:selector];
        id obj;
        while((obj = pFn(enumerator, selector)))
            ; // do nothing
        [tempPool release];
    }
    endTime = clock();
    fprintf(stderr, "done.\nTotal time taken: %f sec\n\n", (float)(endTime - startTime)/CLOCKS_PER_SEC);
    
    [outerPool release];
    return 0;
}

Run the test like so:
    
Hope:~/shell mikeash$ ./enumerator_speed_test 10000000 10
Enumerating through array with 10000000 elements, 10 iterations, using NSEnumerator...done.
Total time taken: 8.810000 sec

Enumerating through array with 10000000 elements, 10 iterations, using for loop...done.
Total time taken: 10.280000 sec

The first parameter is the size of the array to create, and the second array is the number of enumerations to perform for each test.

Which is faster seems to depend on the size of the array. The ~15% variance displayed in the sample run is the largest I've seen in my quickie testing. They're normally very close. NSEnumerator tends to be faster with larger arrays, and the for loop tends to be faster with smaller arrays, which is basically what I expected.

If anybody would like to take this program and do some kind of more detailed study, that would be cool, but hopefully this can put the "No, do it the other way, it's faster!" comments to rest. - MikeAsh

----

;-)

    
./enumerator_speed_test 10000000 10
Enumerating through array with 10000000 elements, 10 iterations, using NSEnumerator...done.
Total time taken: 14.240000 sec

Enumerating through array with 10000000 elements, 10 iterations, using for loop...done.
Total time taken: 16.780001 sec

Enumerating through array with 10000000 elements, 10 iterations, using CFArray for loop...done.
Total time taken: 11.160000 sec

Enumerating through array with 10000000 elements, 10 iterations, using objectAtIndex: function pointer for loop...done.
Total time taken: 11.290000 sec


Thanks for the changes. I've rolled them into the above code, as well as a fifth test that uses NSEnumerator with a cached IMP, which is the fastest at this huge size on my machine. In any case, the differences are mostly small enough to not matter. Chances are you're spending more time working on the objects that are in the array than you are enumerating over the array to begin with, which is what I was kind of trying to show. It's still interesting to see which one wins, of course. - MikeAsh
----
FYI, I was compiling with -O3, just to be fair. Damn, my machine is slow... ;-(

----

Anyone tried to single step through NSEnumerators     nextObject to see what it does? as mentioned elsewhere, NSArray is based on CFArray, and CFArray has no (at least public) interface for enumeration, so I'd think that NSEnumerator just uses     objectAtIndex:.

That said, you choose strategy based on the code, not the speed. Any overhead NSEnumerator has is constant, and it's marginal compared to all the other constant overheads of using a high level language with dynamic dispatch.

