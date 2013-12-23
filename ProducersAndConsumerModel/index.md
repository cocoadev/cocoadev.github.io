---
layout: page
title: ProducersAndConsumerModel
---



----**The producers and consumer model**----

A common situation in multithreaded applications is to want several threads producing data - messages, statistics, etc - and a single thread consuming that data, usually in the order it was produced. For example, imagine an event-logging mechanism that has many threads producing binary event data, and a single thread formatting that data and outputting it to an ascii file ("consuming" the data).

All ProducersAndConsumerModel<nowiki/>s have the potential for PriorityInversion.

One way to implement this is using DistributedObjects, but the overhead of DO is not negligeable and some situations may require a much more responsive, lower-cost solution.

----**The portable solution**----

A simple solution can be written with NSConditionLock. We want the consumer thread to block if the queue is empty, waking only when the queue becomes full again; we will use condition 0 when the queue is empty, and 1 when the queue is full.

    @interface ThreadSafeQueue : NSObject {
  NSMutableArray* elements;
  NSConditionLock* lock; // 0 = no elements, 1 = elements
}

-(id)init;
-(void)enqueue:(id)object;
-(id)dequeue; // Blocks until there is an object to return
-(id)tryDequeue; // Returns NULL if the queue is empty
-(void)dealloc;

@end


    @implementation ThreadSafeQueue

-(id)init {
  if (self = [super init]) {
    elements = [[NSMutableArray alloc] init];
    lock = [[NSConditionLock alloc] initWithCondition:0];
  }
  return self;
}

-(void)enqueue:(id)object {
  [lock lock];
  [elements addObject:object];
  [lock unlockWithCondition:1];
}

-(id)dequeue {
  [lock lockWhenCondition:1];
  id element = [elements objectAtIndex:0] retain] autorelease];
  [elements removeObjectAtIndex:0];
  int count = [elements count];
  [lock unlockWithCondition:(count > 0)?1:0];
  return element;
}

-(id)tryDequeue {
  id element = NULL;
  if ([lock tryLock]) {
    if ([lock condition] == 1) {
      element = [[[elements objectAtIndex:0] retain] autorelease];
      [elements removeObjectAtIndex:0];
    }
    int count = [elements count];
    [lock unlockWithCondition:(count > 0)?1:0];
  }
  return element;
}

-(void)dealloc {
  [elements release];
  [lock release];
  [super dealloc];
}

@end


Note that this simple model actually allows multiple consumers and producers. The following presents an optimized version for a single consumer.

----**The atomic condition solution**----

The code at [[AtomicSQ provides a partial solution to the problem - an atomic queue designed for a single consumer and multiple producers - but we need a little more, because currently if the consumer thread finds the queue empty of data, it will just find itself holding a NULL value. We need some way of sleeping the consumer, and waking it up again when a producer thread puts something back on the queue.

Help is at hand, and its name is, again, NSConditionLock. The *condition* we want the lock to monitor is *the presence or absence of data on the atomic stack* - more specificially, whether data has been inserted on the stack since the last time the consumer thread ran dry. We will denote a dry consumer thread by condition 0, and new data added to an empty queue by condition 1.

The two AtomicSQ functions we need are AtomicSQPush and AtomicSTPQueuePop. We will craft two new functions around these to do the condition lock code.

**KTConsumeEntry**

This function attempts to grab a piece of data off the queue. First, an example of code that uses it:     
// Somewhere globally accessible...
struct message {
    void *volatile reserved; // Reserved for the SQ code
    ...
} ;
void *volatile consumerQueue = NULL;
NSConditionLock *consumerQueueLock;

//Somewhere accessible only by the consumer thread...
void *localCache = NULL;

// In our consumer code...
// Note that consumerQueueLock should have been inited by now
struct message *memory = KTConsumeEntry(&consumerQueue,
                                        &localCache,
                                        consumerQueueLock);
// Do something with memory
free(memory);


This code should be perfectly thread-safe now; if the queue is empty, KTConsumeEntry will sleep the consumer thread until data turns up.

The implementation:

    
void *KTConsumeEntry(void *volatile *consumerQueue,
                     void **lS,
                     NSConditionLock *emptyQueueLock)
{
    void *data = AtomicSTPQueuePop(consumerQueue, lS);
    while (data == NULL)
        {
        [emptyQueueLock lockWhenCondition:1];
        [emptyQueueLock unlockWithCondition:0];
        data = AtomicSTPQueuePop(consumerQueue, lS);
        }
    return data;
}


The code is a simple extension of AtomicSTPQueuePop. Given a queue (with an associated thread-local stack pointer) and a condition lock, we first try popping data off the queue. If there is none, we sleep on the condition lock until another thread indicates there is data available. Once done, we reset the condition lock and pop the data off the queue.

Note that this means we must initialize the condition lock in condition 0, but this is reasonable as NSConditionLock's init method does this automatically.

**KTProduceEntry**

This function attempts to add a piece of data to the end of the queue. First, an example of code that uses it:     
// In our producer code...
// Note that consumerQueueLock should have been inited by now
struct message *memory = malloc(sizeof(struct message));
// Do something with memory
KTProduceEntry(&consumerQueue,
               consumerQueueLock,
               &memory);
memory = NULL; // Make sure we never touch memory again


This code should also be perfectly thread-safe; if the consumer thread is asleep, KTConsumeEntry will wake it up to handle the data.

The implementation:

    
void KTProduceEntry(void *volatile *consumerQueue,
                    NSConditionLock *emptyQueueLock,
                    void *data)
{
    if (AtomicSQPush(consumerQueue, data))
        {   // Wake the consumer
        [emptyQueueLock lock];
        [emptyQueueLock unlockWithCondition:1];
        }
}


This method is even simpler. Given a consumer queue, a condition lock and an entry to put on the queue, we let AtomicSQPush do the hard work of the atomic push. If that function returns YES, the queue was empty when we added the data, so we need to rock the condition lock to condition 1 to wake the consumer thread (or prevent it sleeping, if we are quick enough). Use -lock instead of -lockWithCondition: here is deliberate.

**Provisos**

There are several provisos for this code.

Firstly, since it relies on the AtomicSQ code, it is currently tied down to the PowerPC platform, though the Intel x86 platform (and indeed most modern processor architectures) is perfectly capable of implementing the AtomicSQ functions used.

Secondly, KTConsumeEntry is an "aggregate constant time" method - that means that over many runs, the time taken to execute it will average out as constant. However, there may be occasional latency issues as the code runs down a long queue of stored data that has just been retrieved, looking for the end. (An NSConditionLock alternative could be coded that is true-constant time, give or take the latency in waking a thread.)

Thirdly, this code has now been tested, but an exact comparison with alternative methods of implementing the ProducersAndConsumerModel has not been carried out. Theory suggests this code should be pretty optimal.

Finally, don't forget the many warnings on this site about the inherent problems of SynchronizationPoints; both the methods above have them, so if you find you are calling them a lot you may wish to redesign your code to transfer more in each individual item on the queue, or use other optimizations.

-- Refactored by KritTer; comments appreciated

----
What about performSelectorOnMainThread ?
Does AtomicSQ still works on G5?

--StephaneBoisson

What *about* performSelectorOnMainThread? Please, be more specific.

As for the G5 question, if some asm works on an older PPC, it tends to work on a newer one, or at least that's my theory. We'd have to test on a living, breathing G5 in order to be certain, though, and I know *I* don't have access to one... any volunteers?

-- RobRix

----

--*The following discourse was moved from an unrelated page entitled CodingAnEfficientProxyObject *

It's an interesting idea, Kritter, and you were pretty enlightening on this case. Did you ever see my posts on the cocoadev list or the JediKnight2 source code? The way he did the push from 1 thread to another was more efficient than DO, but it still involved an archival, unarchival, and the use of an NSPort. 

I don't suppose you could whip up a magic trick to efficiently call a selector on an object in another thread with data? :)

-- DaveFayram

Not I. Switching threads is beyond me. You can happily just pass the address of the fast proxy, though, if that speeds up the code - no archive/unarchive needed. But then, you could just do an NSCopy and swear not to touch the copy and have the same thread safety.

However, I **do** have an atomic solution for creating a multithreaded workflow - a thread-safe queue of objects, basically, where one thread "reads" the queue for instructions and other threads add them. Might that be of use?

-- KritTer

That isn't of use with the Appkit, but the general problem of 1 consumer and many producers is something that comes up **all the time**. The way I was doing it was making a mach port associated with a thread, making an object the mach port's delegate, then pushing archived data into the port. This works fine, but I don't know how efficient it is.

I'd love to see your solution though. For time-critical applications like network traffic monitors a very fast model would be very useful. Care to show off your work?

-- DaveFayram

Well, I'll explain the general principle first, then you can tell me if it sounds useful.

Since atomics only work with a single word of memory, we need the shared memory to be a single pointer. Making it the start (top) of a stack is the best idea; NULL represents an empty stack, and any other value a pointer to an information structure, the first word of which is the next pointer. What information you store in the structure is up to you; it could be just an object pointer.

To read the next item off the stack, you atomically swap the top pointer for what it points to (the next item) unless it's NULL in which case you sleep the thread then try again. That makes for a fairly trivial asm function, barely more complicated than the AtomicLockSwap.

To add an item to the stack, you atomically swap in a new pointer and store the old where it points to (the next item); again, this is a trivial asm function.

As long as you never touch an item after putting it on the stack, you are perfectly thread safe and need take no precautions dealing with what you take off the stack, because it is impossible for two threads to take an item off the stack simultaneously.

If you want a queue not a stack, one need only change the read function. It should atomically swap the top pointer for NULL if it points to NULL; otherwise it should just take the top pointer and follow it to find the last item of the queue. If the queue will only be read from by one thread, this is easy because only that thread can access the items in the queue; it can just follow the pointers and slip off the last item of the list with no more atomic operations involved. If the queue is read by multiple threads, the scheme is harder and involves a limited form of atomic locking - swap the queue pointer for NULL, find the end of the removed queue, take that off, then swap the queue back on. This unfortunately lets any new items fall to the front of the queue, but this is unavoidable without a full atomic lock on the queue. The advantage of this limited lock is that threads can still add items to the queue while the read is in progress.

(It is possible to extend all this a bit to allow decent memory allocation schemes and "garbage collection" by adding an "in use" bit to the list items that is set when you start using an item and cleared when you finish, so it is thread-safe to check the "in use" bits to find an unused item. Atomically setting the "in use" bit even allows you to share these items between threads.)

You may have noticed that this scheme needs no locking, not even an atomic one. This means no thread will ever have to wait around for another thread to finish what it is doing (not on the PPC, anyway; Intel chips implement atomics differently) so it is **very** efficient. If you like this system, tell me and I will make a page with all the asm code.

-- KritTer

Well, **very** efficient is much to my liking even if I don't understand anything else on this page. Now that we know the principle... what would I use it for again? :)

-- RobRix

Well, the code's up on AtomicSQ, though I intend to make a more efficient STP queue. What would you use it for? Suppose you want efficient communication between threads; create a comms queue for each thread and pass structures to them from other threads without the overhead of standard ObjC thread communications. Or say you want a shared heap of chunks of memory; just push them all on an atomic stack and pop them off as needed, pushing them back when you're done. It's all about coordinating threads without messing about with expensive overheads - something you need if your threads talk a lot - and unnecessary point of synchronization (when one thread has to stall waiting for another thread to respond) on multi-processor systems.

(As a point of interest, this latter can be minimized by the use of <code>oneway void</code> in protocols; this keyword stops one thread stalling for a response from another when using a standard NSProxy to send ObjC messages between them.)

-- KritTer

----

Having spent a couple of months learning more about this kind of issue, I feel I should add some comments.

Firstly, as I mentioned in SynchronizationPoints, *any* kind of shared memory is expensive in one way or another, and the more SynchronizationPoints you have the worse your performance will be, regardless of how efficiently you craft your code.

More significantly, the atomic queues I have demonstrated do not perform ideally when a queue becomes empty, because the thread that reads from the queue will stall waiting for data. It will not do so in a friendly fashion. I know how to craft a better system, but not with the tools given.

Essentially, the problem boils down to this: you want a thread that has nothing in its queue to sleep, allowing other threads more processor time. If another thread adds to its queue, you want to wake the thread up again. These are non-trivial operations that require OS-level support, and I do not possess that knowledge. If I do, I will write a complete page about using atomics, etc., to create an efficient producer-consumer implementation. Until then, you will either have to use the NSPort-based solutions or put up with threads wasting processor time (spinning) when they have nothing to do :)

-- KritTer

*Until then, you will either have to use the NSPort-based solutions or put up with threads wasting processor time (spinning) when they have nothing to do :)*

This isn't a correct statement. If you have a thread blocked waiting on something like an NSConditionLock it isn't taking any CPU time. Also folks seem to be overly concerned with locking overhead when nine times out of ten it likely wont be the major source a performance problem.

-- Shawn

----

**Example using NSCondition**

Note this class doesn't make any attempt to cleanly wake/terminate waiting consumers when the queue is deallocated. It is only meant as an example.

    @interface ThreadSafeQueue : NSObject {
    NSMutableArray* elements;
    NSCondition* lock;
}

-(id)init;
-(void)dealloc;
-(void)enqueue:(id)object;
-(id)dequeue;       // Blocks until there is an object to return

@end

    @implementation ThreadSafeQueue

-(id)init {
    if (self = [super init]) {
        elements = [[NSMutableArray alloc] init];
        lock = [[NSCondition alloc] init];
    }
    return self;
}

-(void) dealloc {
    [elements release];
    [lock release];
    [super dealloc];
}

-(void) enqueue:(id)object {
    [lock lock];
    [elements addObject:object];
    [lock signal];
    [lock unlock];
}

-(id) dequeue {
    id element;
    
    [lock lock];
    while ([elements count] == 0) [lock wait];
    element = [elements objectAtIndex:0] retain] autorelease];
    [elements removeObjectAtIndex:0];    
    [lock unlock];

    return element;
}

@end

----
Why are you using the private (and therefore unsafe) [[NSCondition instead of the public NSConditionLock? **[This no longer applies as of 10.5. NSCondition is now a public class.]**

----
Sorry to stir an old thread, but what about using CFRunLoop and CFRunLoopSource? Using the NSThread APIs will create a new run loop for you, and then CFRunLoopSourceSignal and CFRunLoopWakeUp can be used to signal new input from the producing thread. CFRunLoop sleeps when it is waiting, so it does a lot of the hard work that KritTer mentioned. The same shared-memory queues can then be used as above.

--QwertyDenzel

