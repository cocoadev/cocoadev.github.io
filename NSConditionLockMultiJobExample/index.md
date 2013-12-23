---
layout: page
title: NSConditionLockMultiJobExample
---

This is an example of use for an NSConditionLock, that came from discussion ExitMainThreadWithAnEmptyWhileLoop.

The class MultiJob starts with an array of jobs, where a job is simply some calculation one can start with     [job runJob], each job being completely independent of the others. To take advantage of multiple processor, these jobs could be run in separate threads. No fancy multithreading here, just the main thread, plus (n-1) secondary threads, all running these jobs one after the other, getting the next job from a common queue.

The number of threads running the jobs is actually stored in the     condition of an     NSConditionLock named threadLock. Before starting the jobs, the condition is set to the number of threads with the code      [threadLock lock]; [threadLock unlockWithCondition:threadCount]; . When a thread is done, it decrements the condition value with a      [threadLock lock];[threadLock unlockWithCondition: ([threadLock condition] - 1) ];. Finally, when the main thread is done runnning the jobs, it waits for the other threads to finish with a      [threadLock lockWhenCondition:0]; [threadLock unlock];.

Here is the code

    

int main (int argc, const char * argv[])
{
    MultiJob *jobs;
    NSArray *someJobs;
    /* ... someJobs are defined ...  */
    jobs=[[MultiJob alloc] initWithJobs:someJobs];
    [jobs runJobsInMultipleThreads:2];
}


@interface MultiJob : NSObject
{
  NSArray *jobArray;
  int nextJobIndex;
  NSLock *nextJobIndexLock;
  NSConditionLock *threadLock;
  BOOL forceRun;
}

//public
- (id)initWithJobs:(NSArray *)anArray;
- (void)runJobsInMultipleThreads:(unsigned int)count;

//private
- (int)nextJobIndex;
- (void)runJobs:(id)sender;

@end


@implementation BCKMultiJob

- (id)initWithJobs:(NSArray *)anArray
{
  self=[super init];
  if (self!=nil) {
    jobArray=[anArray retain];
    nextJobIndex=0;
    nextJobIndexLock=[[NSLock alloc] init];
    threadLock=[[NSConditionLock alloc] init];
    forceRun=NO;
  }
  return self;
}

- (void)dealloc
{
  [jobArray release];
  [nextJobIndexLock release];
  [threadLock release];
  [super dealloc];
}


//manages the job queue by manipulating nextJobIndex inside a lock
- (int)nextJobIndex
{
  int result=-1;
  [nextJobIndexLock lock];
  if (nextJobIndex<[jobArray count]) {
    result=nextJobIndex;
    nextJobIndex++;
  }
  [nextJobIndexLock unlock];
  return result;
}

//each thread will run that code, including the main thread
- (void)runJobs:(id)sender;
{
  NSAutoreleasePool *pool;
  BCKJob *aJob;

  //run jobs from the queue until none left
  pool=[[NSAutoreleasePool alloc] init];
  int i=[self nextJobIndex];
  while (i>=0) {
    aJob=[jobArray objectAtIndex:i];
    [aJob runJob];
    i=[self nextJobIndex];
  }
  [pool release];
  
  //the thread is done with the jobs
  //decrease the count of threads in threadLock
  [threadLock lock];
  [threadLock unlockWithCondition: ([threadLock condition] - 1) ];
}


- (void)runJobsInMultipleThreads:(unsigned int)count
{
  int i;
  
  //initialize the threadLock with the number of thread
  [threadLock lock];
  [threadLock unlockWithCondition:count];
  
  //start the secondary threads
  //loop starts at 1 because the main thread is also used (see below)
  for (i=1;i<count;i++)
    [NSThread detachNewThreadSelector:@selector(runJobs:)
                 toTarget:self
                 withObject:self];
  
  //start the jobs for the main thread
  [self runJobs:self];
  
  //wait until done, ie until the count of threads in threadLock is down to 0
  [threadLock lockWhenCondition:0];
  [threadLock unlock];
}


@end


