---
layout: page
title: NSLockVsSynchronized
---

One and all.

I've  been wondering for some time how different locking mechanisms compare to each other in terms of speed, and since I was working on some code today where this is an issue, I decided it was about time to get some conculsive evidence.  I need recursive locking really, but I wanted to compare the speed of using a non-recursive lock but calling it one time after the other (to make the test fair).  I should point out that whilst it's obvious that objective-c messaging has an overhead, and that some of that can be attributed to the results, my initial intention was to debunk the myth about how @synchronized compared to a straight NSLock, I added  the other tests later for the sake of completeness.  Results were as follows:

    
[Session started at 2006-03-27 14:38:30 +0200.]
2006-03-27 14:38:30.694 LockSpeedTests[8246] @syncronized test
2006-03-27 14:38:34.633 LockSpeedTests[8246] Took 3.93771 seconds to complete
2006-03-27 14:38:34.645 LockSpeedTests[8246] NSRecursiveLock test
2006-03-27 14:38:36.002 LockSpeedTests[8246] Took 1.34898 seconds to complete
2006-03-27 14:38:36.009 LockSpeedTests[8246] NSLock test
2006-03-27 14:38:36.970 LockSpeedTests[8246] Took 0.94935 seconds to complete
2006-03-27 14:38:36.978 LockSpeedTests[8246] pthread_mutex test
2006-03-27 14:38:37.437 LockSpeedTests[8246] Took 0.455432 seconds to complete
2006-03-27 14:38:37.445 LockSpeedTests[8246] pthread_mutex (recursive) test
2006-03-27 14:38:37.836 LockSpeedTests[8246] Took 0.387441 seconds to complete
LockSpeedTests has exited with status 0.


The program i used to obtain the results was the following:

    

#import <Foundation/Foundation.h>
#import <pthread.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
    
    NSNumber *number = [NSNumber numberWithInt:1];
    unsigned limit = 1000000U;
    
    // @syncronized...
    NSLog (@"@syncronized test");
    NSDate *start = [NSDate date];
    for ( unsigned i = 0; i < limit; i++ )
    {
        @synchronized ( number )
        {
            @synchronized ( number )
            {
                @synchronized ( number )
                {
                    [number intValue];
                }
            }
        }
    }
    NSLog (@"Took %g seconds to complete",[[NSDate date] timeIntervalSinceDate:start]);
    
    // NSRecursiveLock...
    NSLog (@"NSRecursiveLock test");
    NSRecursiveLock *rlock = [[NSRecursiveLock alloc] init];
    start = [NSDate date];
    for ( unsigned i = 0; i < limit; i++ )
    {
        [rlock lock];
        [rlock lock];
        [rlock lock];
        [number intValue];
        [rlock unlock];
        [rlock unlock];
        [rlock unlock];
    }
    NSLog (@"Took %g seconds to complete",[[NSDate date] timeIntervalSinceDate:start]);
    
    // NSLock...
    NSLog (@"NSLock test");
    NSLock *lock = [[NSLock alloc] init];
    start = [NSDate date];
    for ( unsigned i = 0; i < limit; i++ )
    {
        [lock lock];
        [lock unlock];
        [lock lock];
        [lock unlock];
        [lock lock];
        [number intValue];
        [lock unlock];
    }
    NSLog (@"Took %g seconds to complete",[[NSDate date] timeIntervalSinceDate:start]);
    
    // pthread mutex
    pthread_mutex_t mutex;
    pthread_mutex_init( &mutex, NULL );
    NSLog (@"pthread_mutex test");
    start = [NSDate date];
    for ( unsigned i = 0; i < limit; i++ )
    {
        pthread_mutex_lock( &mutex );
        pthread_mutex_unlock( &mutex );
        pthread_mutex_lock( &mutex );
        pthread_mutex_unlock( &mutex );
        pthread_mutex_lock( &mutex );
        [number intValue];
        pthread_mutex_unlock( &mutex );
    }
    NSLog (@"Took %g seconds to complete",[[NSDate date] timeIntervalSinceDate:start]);
    
    // pthread recursive mutex
    NSLog (@"pthread_mutex (recursive) test");
    pthread_mutexattr_t attributes;
    pthread_mutexattr_settype( &attributes, PTHREAD_MUTEX_RECURSIVE );
    pthread_mutex_t rmutex;
    pthread_mutex_init( &rmutex, &attributes );
    start = [NSDate date];
    for ( unsigned i = 0; i < limit; i++ )
    {
        pthread_mutex_lock( &rmutex );
        pthread_mutex_lock( &rmutex );
        pthread_mutex_lock( &rmutex );
        [number intValue];
        pthread_mutex_unlock( &rmutex );
        pthread_mutex_unlock( &rmutex );
        pthread_mutex_unlock( &rmutex );
    }
    NSLog (@"Took %g seconds to complete",[[NSDate date] timeIntervalSinceDate:start]);

    pthread_mutexattr_destroy( &rmutex );
    pthread_mutex_destroy( &mutex );
    
    [pool release];
    return 0;
}



I hope this is of use to someone else.  For me it proved that I should use recursive pthread mutexes when speed was important.  I think that the overall lesson is not that @synchronized is inherently bad, just that different locking techniques suit different situations and it's important to evaluate the context before settling on a solution.

JKP
----
Apple: *In addition to lock classes, the Objective-C language includes the @synchronized directive for locking a block of code. The directive takes a single parameter, which is the object you want to be used as the key for locking the code. The compiler then creates a mutex lock based on that object. Threads attempting to lock the same object block until the current synchronized block finishes executing.*

Just to make sure I have this straight, is     [someLock lock] the same as     @synchronized (someLock)? If so, does     @synchronized take a bit of extra time to lock the NSNumber (which is not usually lockable)? --JediKnil

----
Answer:  locking via NSLock and locking via @syncronized are completely seperate.  The advantage of @sync is its ease of use i.e., no need to explicitly create a lock for a particular ivar or chunk of code, just wrap it with the directive.  If you wanted to lock it with an NSLock, you'd need 1 NSLock instance per thing or bit of code you want to load (granularity is generally good with locking since it makes things go faster) which can mean a heap of extra ivars etc.  Summary: If speed isn't important go with @sync.

----
And, just to remind people: *don't worry about speed* until you've positively identified a bottleneck through performance testing; once you have, keep using performance testing to ensure the changes you're making are actually helping matters. Performance is rarely a simple thing, and a benchmark like the above bears little relevance to real code performance (as I'm sure the poster knows).

Use the idiom that lets you code with the fewest errors, and use known algorithms with the best scaling characteristics. As AmdahlsLaw shows, little things like a factor-of-ten speedup make no worthwhile difference if they're only in effect 0.5% of the time.

----

I think every one interested should read this: http://googlemac.blogspot.com/2006/10/synchronized-swimming.html it is about @synchronized() keyword, written by a google mac guy!

----

If anyone really needs a thread-safe singleton, you should seriously consider creating it in     +initialize rather than going through all the pain and uglitude of that solution. You'll be faster too, once you've finished the initial setup!

---- They didn't use     +initialize because they were working in a category. After a false start using swizzling to remove unnecessary synchronizing, they settled on GCC's constructor attribute: http://googlemac.blogspot.com/2006/11/getting-loaded.html

So if you can't use     +initialize, all is not lost; if you can, though, do. -- RobRix

