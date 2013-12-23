---
layout: page
title: MapReduce
---

With the increase in multi core machines and the example by google and their distributed MapReduce implementation( http://labs.google.com/papers/mapreduce.html ) why not have the same thing but on a local multiprocessor machine. 

**HOM integration**
This also maps well onto HOM where a map, select and reduce message could distribute the calls automatically to all the different cores(threads).

**Single core machines**
Even on a single processor machine there might be advantage to using this. For example in the case of processing many different files. Whilst one thread is waiting for IO to complete others can be completing their computation. All of this can complete.

**Dead lock and race conditions**
All messages passed must be written in an re-enterent manner.

**ThreadPool**
I've seen this kind of thing somewhere before but I cannot remember where. A thread pool is a pool of threads which can be called upon to do your bidding. 

The pool has a maximum size and starts with no threads in it. Once an (object, message) pair is passed to it a thread is created and then given the object and message. Once a message has completed the thread waits for more input. The next (message, object) pair to be received by the pool is sent to the first thread which is waiting for input, a new thread is created if the pool isn't full or otherwise the pair is left on a queue till a thread becomes free.

Problem: There is no way of making sure a thread is running on a unique cpu portably at least.  

Why bother this and not just create a new thread when ever you need them. Well to save time creating a thread for a relatively small amount of work. With an efficient queue particularly a atomic queue atomicSQ (I think i'm not sure how the blocking would work).

Another thing is that each thread must have it's one thread pool. The reason for this is to stop a thread sending a job to the same pool which is in. If that thread is the only thread in the pool and it is waiting for that job to complete then it will have dead locked. So like autorelease a thread pool is lock to the thread which it is running it. This does mean that it is possible that each thread could produce a large number of other threads and then these threads create even more threads. This is possible and may happen but as the jobs being passed to the pool would probably be relatively small it's unlikely.

Other idea: Thread pools can borrow threads from other pools when they are sitting idle. In stead of creating a new thread a pool could ask other pools if they have any free threads which they could have saving on setup time and reducing the total number of threads created.

A problem: The auto-release pool for a thread. Each thread will need an auto-release pool (Not a problem in the future with GC). 

Q. When should we release the threads autorelease pool? 
A. Every time it completes a job? Seems like the best time but if it's a really short job then we could be spending a lot of time de-allocating small amounts of memory. 

Q. Is there a way to see the size of the zone which the pool has allocated and release the pool when it reaches some limit? 
A. Haven't looked and probably too complicated

Q. Should we really be bothered about this? 
A. Probably shouldn't bother but it may depend on how people would tend to use it.

**ThreadPool Interface**
BSThreadPool
The pool it's self. There is no reason the thread pool needs to be made up of threads on the local machine. These could be threads on distant machines and the object and job gets sent to the other machine to be run.
    
@interface BSThreadPool : NSObject {
	  NSMutableArray* iThreads;
	  BSQueue* iWork;
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
-(BSCompletionNotice*) invoke:(NSInvocation*)pInvocation;

/*!
	  @method invoke:on:
	  @description Executes the invocation on the first free thread it finds.
	  @param pSelector The selector to send to the target on a free thread.
	  @param pTarget The target to send the selector to.
	  @result A completion notice which can be used to inform the recieve of when the job is completed.
*/
-(BSCompletionNotice*) invoke:(SEL)pSelector on:(id)pTarget;

/*!
	  @method end
	  @description Removed the pool from the stack of current threads pools. Also kills all the threads.
	  The threads will complete the job they are currently working on and then end. If there are jobs still
	  to be started they may not be completed.
*/
-(void)end;


BSCompletionNotice
This is used to wait for jobs to finish and to receive any exceptions.
    
@interface BSCompletionNotice :NSObject{
	  BSThreadPoolJob* iJob;
}
- (id) initWith:(BSThreadPoolJob*)pJob;
/*!
	  @method exceptions
	  @description Returns any exceptions which occured when running the job.
	  @result An exception of nil if there wheren't any exceptions.
*/
- (id) exception;
- (BOOL) isFinished;
- (BOOL) hasStarted;
- (void) waitOn;
- (void) getReturnValue:(void *)buffer;
@end


**Multithread HOM Interface**
I would put my HOM interface on to NSEnumerator because I prefer it here and I think it's cleaner. Lots of people disagree, I know. It is also then added to NSArray, NSDictionary too but as NSEnumerator is a standard iteration class then we've covered all/most bases.
    
@interface NSEnumerator (BSHOM)
- (id) foldl;
- (id) map;
- (id) select;
- (id) reject;
- (id) threadedMap:(int)pNumThreads;
- (id) threadedSelect:(int)pNumThreads;
- (id) threadedReject:(int)pNumThreads;
// Not yet implemented
- (id) threadedfoldl:(int)pNumThreads;
@end

I don't think pNumThreads is a good way to do it. It should just use as many threads as possible. Also in my implementation it's a misnomer which I haven't yet corrected. It's really the number of jobs it's split into. These jobs are then consumed by the threads in the thread pool. 

**Example of use**
This is the main function of my multithreaded HOM implementation. It's more an example of using HOM. Also it is actually slower on my machine then if I use the single threaded version but I only have a PowerBook. It may be better for problems which involve larger chunks of disk reads I haven't tried it.

    
int main(int argc, char *argv[])
{
	  NSAutoreleasePool* tPool = [[NSAutoreleasePool alloc] init];
	  BSThreadPool* tThreadPool = [BSThreadPool threadPoolOfSize:3];
	  BSArguments* tArgument = [[BSArguments alloc] initWith:argc arguments:argv];
	  
	  NSDate* tTime = [NSDate date];
	  NSArray* tResults = [[[tArgument files] threadedMap:3] grep:[tArgument regex] options:kIncludeLineNumber | kIncludeFileName] foldl] arrayByAddingObjectsFromArray:nil];	  

	  float tTimeInt = [tTime timeIntervalSinceNow];
	  	  
	  foreach (tResult, tResults){
			printf("%s\n", [tResult cString]);
	  }
	  [[NSLog(@"Time: %f", tTimeInt);
	  
	  [tThreadPool end];
	  [tArgument release];
	  [tPool release];
	  return 0;
}


Wonder what people think and has all this been done before?

Stefan Pantos

----
Release your pool with every job. Autorelease pools are *cheap*. They cost about as much to create and destroy as it does to create and destroy two plain NSObject<nowiki/>s. In other words, the time spent is totally inconsequential. Unless your jobs are on the order of     return 1+1 you'll be spending far more time processing those, and most likely your thread communication system will have a lot more overhead too. There's no point in getting fancy.

Otherwise I think this is a good idea, but unfortunately it's much more complicated than it would be in a functional language. You have to make sure that all the code that goes through the map/reduce system is thread safe, preferably by having no side effects. But ObjC really encourages the use of side effects so you'll have to be careful. Still, I think it could be a real aid to getting the most performance out of these shiny new multicore machines.

----
Just been looking though the Java API and it provides a thread pool which gives a similar interface http://java.sun.com/j2se/1.5.0/docs/api/java/util/concurrent/ThreadPoolExecutor.html I haven't yet seen an implementation of the multi threaded map reduce etc.

What would also be possible with this interface is that the threads wouldn't have to be local. It could provided a clean interface which would give access to 
a cluster of machines in the same was as google do map reduce just with what I think is a much cleaner interface.

I have a working implementation now and I am just implementing a partially computed array. This will give access to elements of a map result which have already been computed and block when accessing unavailable ones till they are available. What this allows you do is in the case of a map and a reduce (foldl in my version) is start the reduce before the map has completed.

Stefan

----
Here is some example code (http://www.beaversoft.co.uk/programming/ThreadedHOM.html) it's part of a simple grep program. I would be interested to find out if it is at all faster on a multi core machine if anyone has one. You also need to get a copy of my thread pool code here(http://www.beaversoft.co.uk/programming/ThreadPool.html). I haven't written much about it yet and I'm sure there are bugs in it. Any suggestions would be appreciated.

If you do have a multicore/CPU machine and you manage to get it to compile and work could you please tell me how it performs. if you use don't use -t it uses a no extra threads.

Stefan

