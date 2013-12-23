---
layout: page
title: ThreadPool
---

I originally came up with the idea of a thread pool to give an easy way to distribute computations to different thread (possible cores) particually in the case of General/MapReduce. Later I discovered that the mighty Java API came up with it too. http://java.sun.com/j2se/1.5.0/docs/api/java/util/concurrent/General/ThreadPoolExecutor.html

Nice to know that it's not a completely stupid idea but I wish it was original.

Anyway the interface I came up with was this:
    
@interface General/BSThreadPool : General/NSObject {
	  General/NSMutableArray* iThreads;
	  General/BSQueue* iWork;
	  unsigned short iCreatedThreads;
	  unsigned short iMaxThreads;
}
/*!
  @method currentThreadPool
  @description Returns the current thread pool or nil if one hasn't been created for this thread.
*/
+ (id) currentThreadPool; 

/*!
  @method threadPool
  @description Returns a new thread pool with the same number of threads as thier are CPU's 
  and makes it the current thread pool.
  @param pNumThreads. The number of threads for the pool to contain.
*/
+ (id) threadPool;

/*!
  @method threadPool:
  @description Creates a new thread pool with the number of threads specified 
  and makes it the current thread pool.
  @param pNumThreads. The number of threads for the pool to contain.
  @result A newly created autoreleased thread pool or nil on error.
*/
+ (id) threadPoolOfSize:(unsigned int)pPoolSize;

/*!
@method initPoolOf:
@description Initialises a thread pool and makes it the current thread pool.
@param pNumThreads. The number of threads for the pool to contain.
@result The initialised thread pool with the specified number of threads.
*/
-(id) initPoolOf:(unsigned int)pPoolSize;

/*!
	  @method invoke:
	  @description Executes the invocation on the first free thread it finds.
	  @param pInvocation the invocation to run on a thread. The invocation should be ready to run.
	  @result A completion notice which can be used to inform the recieve of when the job is completed.
*/
-(General/BSCompletionNotice*) invoke:(General/NSInvocation*)pInvocation;

/*!
	  @method invoke:on:
	  @description Executes the invocation on the first free thread it finds.
	  @param pSelector The selector to send to the target on a free thread.
	  @param pTarget The target to send the selector to.
	  @result A completion notice which can be used to inform the recieve of when the job is completed.
*/
-(General/BSCompletionNotice*) invoke:(SEL)pSelector on:(id)pTarget;

/*!
	  @method end
	  @description Removed the pool from the stack of current threads pools. Also kills all the threads.
	  The threads will complete the job they are currently working on and then end. If there are jobs still
	  to be started they may not be completed.
*/
-(void)end;

- (unsigned short) capacity;
@end


To do a asynchronous call such as initialise an object with the first 200 primes we could do something like this: (Bit contrived but it's to make a point)
    
First100Primes* tPrimes1 = [First100Primes alloc];
FirstNext100Primes* tPrimes2 = [First100Primes alloc];
General/BSThreadPool* tPool = General/[BSThreadPool threadPool];

General/BSCompletionNotice* tCompletion1 = [tPool invoce:@selector(init) on:tPrimes1]; 
General/BSCompletionNotice* tCompletion2 = [tPool invoce:@selector(init) on:tPrimes2];
\\ Do some more stuff more stuff without the need of the tPrimes
tPrimes1 = [tCompletion1 getResult]; // if it's not completed yet it blocks.
\\ more stuff using tPrimes1
tPrimes2 = [tCompletion1 getResult]; 


Now we can do some other stuff using come ideas from HOM. So we add the async message General/NSObject. This creates a General/BSASync object which forwards all calls to the original object but in a new thread in the current thread pool. And they all return a General/BSCompletionNotice. The above code becomes.
    
First100Primes* tPrimes1 = [First100Primes alloc];
FirstNext100Primes* tPrimes2 = [First100Primes alloc];
General/BSThreadPool* tPool = General/[BSThreadPool threadPool];

General/BSCompletionNotice* tCompletion1 = General/tPrimes1 async] init]; // Much more simple
[[BSCompletionNotice* tCompletion2 = General/tPrimes2 async] init]; 
\\ Do some more stuff more stuff without the need of the tPrimes
tPrimes1 = [tCompletion1 getResult]; // if it's not completed yet it blocks.
\\ more stuff using tPrimes1
tPrimes2 = [tCompletion1 getResult]; 


At some point I plan to post up the rest of the interfaces and the code at some point.

Stefan Pantos.
----
I have finally got some example code up. I'm sure there will be bugs in it and there are better ways to do stuff. Any suggestions would be appreciated. http://www.beaversoft.co.uk/programming/[[ThreadPool.html Also if the text doesn't make sense, i'm sorry. I'm really bad at writing particularly at the moment as I'm trying to write up.

Stefan
