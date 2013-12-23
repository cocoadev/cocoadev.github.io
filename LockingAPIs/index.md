---
layout: page
title: LockingAPIs
---

Mac OS X contains various locking APIs with different capabilities and interfaces. This page examines four locking strategies that would be suitable for writing thread-safe AccessorMethods, although they can all be used for other situations as well.

Writing thread-safe AccessorMethods is almost never the right way to achieve ThreadSafety. The granularity is far too low and it's normally far more productive to use a larger model-specific locking strategy instead of protecting accessors. With that in mind, however, here are some ways to do it if you wanted to.

All tests were performed on a PowerBook G4/1.5, and timings reflect that. See the end of the page for the test program. http://goo.gl/OeSCu

----

**NSLock**

NSLock is the base Cocoa lock class. It has a simple interface and is nice and Cocoa-y. It is a straightforward mutex with no bells or whistles.

The NSLock setter took 0.69 microseconds per invocation.

The NSLock getter took 1.09 microseconds per invocation. This is longer because the set method has the extra overhead of autoreleasing the object it returns.

----

**pthread_mutex**

pthread_mutex is the basic pthreads mutex. It's like the C/POSIX equivalent to NSLock. It doesn't offer anything terribly special (although it can be set up to work like an NSRecursiveLock if desired) but it's a bit faster than NSLock by virtue of being a bit more low-level.

The pthread_mutex setter took 0.58 microseconds.

The pthread_mutex getter took 0.95 microseconds.

----

**pthread_rwlock**

pthreads provide a read/write lock. Accessors don't need absolute mutual exclusion. It's perfectly fine if the getter runs in two threads simultaneously. But when the setter is running nothing else may be running, not a setter or a getter. pthread_rwlock lets you do this. It has two ways to lock it, one which locks for reading and allows sharing, and one which locks for writing and forces everybody else to wait.

All of this fancy stuff comes with extra overhead, making this lock the slowest of the bunch. Further, readers are still serialised in actually obtaining the lock for read access on a multiprocessor system. However, it can provide better overall performance for a specific pattern of accesses: if the lock is frequently obtained for reading, and if readers hold the lock for a long time, the extra parallelism gained by allowing these read-only critical sections to overlap can outweigh the one-time cost of grabbing the lock. (Ensuring the lock is stored in a different cacheline from the data it is protecting will improve performance in this situation on a multiprocessor.)

The possibility of this ever happening for a simple accessor is extremely doubtful, but it can come in handy for other applications.

The pthread_rwlock setter took 0.77 microseconds.

The pthread_rwlock getter took 1.12 microseconds.

----

**OSSpinLock**

Buried inside the scarily-named     libkern directory is the     OSAtomic.h header. It has all kinds of goodies in it for lockless thread-safe programming, and it also has OSSpinLock. A spin lock is a lock that, effectively, operates by repeatedly asking "is it free yet? is it free yet? is it free yet?" until the lock is free, and then running in. The major difference is that it never calls into the kernel. This means that it has less overhead than any of the other locks presented, but the locking thread can't be put to sleep if the lock is taken in such a way that it can be woken up when the other thread unlocks. It is basically a polling solution and as such can be much less efficient when contention happens. We don't anticipate much contention for accessors, so the decreased overhead should provide an overall speed gain.

The OSSpinLock setter took 0.48 microseconds.

The OSSpinLock getter took 0.89 microseconds.

----

**Conclusion**

OSSpinLock comes in as the fastest, but not by much. This is because pthread_mutex is smart enough to try a spinlock first. It only kicks into the kernel if the lock is already taken by another thread, so in the no-contention case it barely has to do any more work than OSSpinLock. Since pthread_mutex has none of the downsides of OSSpinLock there isn't much reason to use OSSpinLock instead.

Overall, the time taken with actual work dominates even in something as simple as an accessor. The fastest setter is only about 40% faster than the slowest, and the fastest getter is only 20% faster than the slowest. It's hard to think of something that would be less work than an accessor and still need locking, so it would seem that which type of lock you choose will generally not have a performance impact.

And as stated above, locking in your accessors is the wrong route to take anyway. But now you have all the information you need on the different locking APIs you can use to do it.

----

**Code**

This program was used to get all of the timings quoted above.

    
#import <objc/objc-runtime.h>
#import <libkern/OSAtomic.h>
#import <pthread.h>
#import <Foundation/Foundation.h>

@interface TestClass : NSObject {
    pthread_mutex_t mutex;
    pthread_rwlock_t rwlock;
    OSSpinLock spinlock;
    NSLock *nslock;
    id ivar;
}
- (void)setIvarMutex: (id)newVal;
- (void)setIvarRwlock: (id)newVal;
- (void)setIvarSpinlock: (id)newVal;
- (void)setIvarNSLock: (id)newVal;
- (id)getIvarMutex;
- (id)getIvarRwlock;
- (id)getIvarSpinlock;
- (id)getIvarNSLock;
@end

@implementation TestClass
- init
{
    pthread_mutex_init( &mutex, NULL );
    pthread_rwlock_init( &rwlock, NULL );
    spinlock = OS_SPINLOCK_INIT;
    nslock = [[NSLock alloc] init];
    return self;
}

- (void)setIvarMutex: (id)newVal
{
    pthread_mutex_lock( &mutex );
    [newVal retain];
    [ivar release];
    ivar = newVal;
    pthread_mutex_unlock( &mutex );
}

- (void)setIvarRwlock: (id)newVal
{
    pthread_rwlock_wrlock( &rwlock );
    [newVal retain];
    [ivar release];
    ivar = newVal;
    pthread_rwlock_unlock( &rwlock );
}

- (void)setIvarSpinlock: (id)newVal
{
    OSSpinLockLock( &spinlock );
    [newVal retain];
    [ivar release];
    ivar = newVal;
    OSSpinLockUnlock( &spinlock );
}

- (void)setIvarNSLock: (id)newVal
{
    [nslock lock];
    [newVal retain];
    [ivar release];
    ivar = newVal;
    [nslock unlock];
}

- (id)getIvarMutex
{
    pthread_mutex_lock( &mutex );
    id tmp = ivar retain] autorelease];
    pthread_mutex_unlock( &mutex );
    return tmp;
}

- (id)getIvarRwlock
{
    pthread_rwlock_rdlock( &rwlock );
    id tmp = [[ivar retain] autorelease];
    pthread_rwlock_unlock( &rwlock );
    return tmp;
}

- (id)getIvarSpinlock
{
    [[OSSpinLockLock( &spinlock );
    id tmp = ivar retain] autorelease];
    [[OSSpinLockUnlock( &spinlock );
    return tmp;
}

- (id)getIvarNSLock
{
    [nslock lock];
    id tmp = ivar retain] autorelease];
    [nslock unlock];
    return tmp;
}

@end

double [[GetSecs( void )
{
    struct timeval tv;
    gettimeofday(&tv, NULL);
    
    return tv.tv_sec + tv.tv_usec / 1000000.0;
}

void TestSetSpeed( int iters, id obj, SEL setSel, id val )
{
    int i;
    
    printf( "Testing %d iterations with %s...", iters, setSel );
    fflush( stdout );
    
    double startTime, endTime;
    
    startTime = GetSecs();
    
    for( i = 0; i < iters; i++ )
    {
        ((void (*)(id, SEL, id))objc_msgSend)( obj, setSel, val );
    }
    
    endTime = GetSecs();
    
    double total = endTime - startTime;
    double per = total / iters;
    
    printf( "done\ntotal time: %f seconds, %f usec per set\n\n", total, per * 1000000 );
    fflush( stdout );
}

void TestGetSpeed( int iters, id obj, SEL getSel )
{
    int i;
    
    printf( "Testing %d iterations with %s...", iters, getSel );
    fflush( stdout );
    
    double startTime, endTime;
    
    startTime = GetSecs();
    
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    for( i = 0; i < iters; i++ )
    {
        ((id (*)(id, SEL))objc_msgSend)( obj, getSel );
        if( i % 100 == 0 )
        {
            [pool release];
            pool = [[NSAutoreleasePool alloc] init];
        }
    }
    [pool release];
    
    endTime = GetSecs();
    
    double total = endTime - startTime;
    double per = total / iters;
    
    printf( "done\ntotal time: %f seconds, %f usec per get\n\n", total, per * 1000000 );
    fflush( stdout );
}

int main(int argc, char **argv)
{
    TestClass *testObj = [[TestClass alloc] init];
    NSObject *obj = [[NSObject alloc] init];
    TestSetSpeed( 10000000, testObj, @selector( setIvarMutex: ), obj );
    TestSetSpeed( 10000000, testObj, @selector( setIvarRwlock: ), obj );
    TestSetSpeed( 10000000, testObj, @selector( setIvarSpinlock: ), obj );
    TestSetSpeed( 10000000, testObj, @selector( setIvarNSLock: ), obj );
    
    TestGetSpeed( 10000000, testObj, @selector( getIvarMutex ) );
    TestGetSpeed( 10000000, testObj, @selector( getIvarRwlock ) );
    TestGetSpeed( 10000000, testObj, @selector( getIvarSpinlock ) );
    TestGetSpeed( 10000000, testObj, @selector( getIvarNSLock ) );
}

