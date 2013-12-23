---
layout: page
title: ExitMainThreadWithAnEmptyWhileLoop
---

I am trying to run a very simple multithreaded tool. I have an array of jobs, where a job is simply some calculation I can start with     [job runJob], which takes several minutes on average for each job. To take advantage of multiple processor, I decided to make this very simple     MultiJob class. I am not looking for fancy multithreading here. I know there are plenty of super-robust multithreading classes out there, but I want to understand what is going on in my particular class.

The first question: is it true that I need two threads to take advantage of two processors? If the answer is NO, well, maybe I can just forget about the whole thing, but I would still like to understand.

Now, if the answer is YES, I definitely have to solve the problem with MultiJob.

Here is how it works. When the method     runJobsInMultipleThreads:(int)count is started, the instance variable     threadCount is set to     count, then     count threads are started with the method     runJob:, the main thread being the last to be started. Each thread ask for the next job index available (with the method     nextJobIndex), run it, ask again, etc... until no more jobs are available, at which point it decreases the     countThread variable (done inside a NSLock) and returns.
When the main thread finishes, it has to wait for the other(s) thread(s) to finish. For this, I simply have a '    while (threadCount>0)'. The problem is I apparently have to put some code inside that while loop for the program to behave properly. More  precisely, if I just use '    while (threadCount>0) {;} ', and if the main thread finishes first, it gets into the loop but never gets out of it, even though the NSLog shows that threadCount DOES reach 0 at some point. If the main thread finishes last, the while loop exits without any problem (actually never enters, I guess).

Below is the code that works: I had to put some silly code inside the waiting loop. I suspect that it has to do with the compiler changing '    while (threadCount>0) {;} ' into a unique instruction (something like 'block forever' vs 'you can go') because it does not realize that the value of threadCount can change, even without any code inside the while.

So what should I do to make it work without some dummy code inside the while loop? I would keep it like that, but the program has to run on several computers, some single, some dual processors, and I worry that the code in the while loop might slow down the program when I run it in a single processor. Also, silly code looks stupid, and I want to get rid of it.

Thanks for your help! CharlesParnot

    

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
	int threadCount;
	NSLock *myLock1,*myLock2;
}

- (id)initWithJobs:(NSArray *)anArray;
- (int)nextJobIndex;
- (void)runJobs:(id)sender;
- (void)runJobsInMultipleThreads:(unsigned int)count;

@end

@implementation MultiJob

- (id)initWithJobs:(NSArray *)anArray
{
	self=[super init];
	if (self!=nil) {
		jobArray=[anArray retain];
		nextJobIndex=0;
		threadCount=0;
		myLock1=[[NSLock alloc] init];
		myLock2=[[NSLock alloc] init];
	}
	return self;
}

- (void)dealloc
{
	[jobArray release];
	[myLock1 release];
	[myLock2 release];
}

- (int)nextJobIndex
{
	int result=-1;
	[myLock1 lock];
	if (nextJobIndex<[jobArray count]) {
		result=nextJobIndex;
		nextJobIndex++;
	}
	[myLock1 unlock];
	return result;
}

- (void)decreaseThreadCount
{
	[myLock2 lock];
	threadCount--;
	[myLock2 unlock];
	NSLog(@"threadCount = %d",threadCount);
}

- (void)runJobs:(id)sender
{
	NSAutoreleasePool *pool;
	BCKJob *aJob;
	pool1=[[NSAutoreleasePool alloc] init];
	int i=[self nextJobIndex];
	while (i>=0) {
		aJob=[jobArray objectAtIndex:i];
		[aJob runJob];
		i=[self nextJobIndex];
	}
	[pool release];
	[self decreaseThreadCount];
}


- (void)runJobsInMultipleThreads:(unsigned int)count
{
	int i;
	threadCount=count;
	//start the separate threads
	for (i=1;i<count;i++)
		[NSThread detachNewThreadSelector:@selector(runJobs:)
								 toTarget:self
							   withObject:self];
	//start the main thread
	[self runJobs:self];
	//wait until done
	while(threadCount>0) {
		/* !!!! It only works if I put this silly code !!!! */
		if (threadCount==0)
			threadCount=0;
	}
}

@end



----

You seem to be suffering under a mis-conception. Your main thread is actually running from the moment you start the program, and will continue running until the program ends. Each thread you detach also starts running immediately. There is no need to start any threads.
*Oups, my comments in the code are a bit misleading. I meant 'put the main thread to work on those jobs'. This is why I start with i=1 (see below), the main thread being the extra thread and being treated just like any other thread... at least until it is finished.* CharlesParnot

If you want to end the program when all your job threads have finished, use a counter and two locks. The counter will keep track of how many jobs are running, one lock will protect the counter, and the other lock will block the main thread until the jobs have finished. When a job finishes, it should update the counter and see if the main thread can be unblocked.

I also spotted a bug in the above code. The "start the separate threads" for loop is wrong; you should either start with i=0 (in the first term) or run while i<=count (in the second term).
*Again, I should have commented that better. I start the loop at 1, because I include the main thread as one of the thread in threadCount. It is a bit confusing, but I really want this class to be just working, and not doing anything else like run loops and thread messaging. I guess not including the main thread in threadCount could also be confusing, anyway.* CharlesParnot

�DustinVoss
----
Actually, the counter and two locks can be replaced with a single NSConditionLock (semaphore).

Also, I will assume he understands that he is not starting the main thread. He is just including the main thread in job processing; that is also why he loops from 1. The main thread is the extra thread.
*Thanks for understanding me so well!! You are right, this is what I meant.* CharlesParnot

One bug I noticed is that MultiJob's dealloc method does not call [super dealloc].
*Arrrgh. I keep forgetting this one in half of my classes. Thanks.* CharlesParnot

Again, this could be cleaned up greatly with an NSConditionLock (hypothetically named m_threadLock here) initialized to the thread count. The loop waiting for the threads to exit would be replaced with      [m_threadLock lockWhenCondition:0], [m_threadLock unlock] . Before the thread method exits, call      [m_threadLock lock], [m_threadLock unlockWithCondition:[m_threadLock condition] - 1] .

*Hah, I *knew* there was a semaphore in there somewhere! I had forgotten that NSConditionLock takes integers.*

----
Finally, after more than 24 hours of inactivity, I got what I needed. This site is great. Thanks very much!
I thought I would have to use NSConditionLock, but things were still quite unclear to me. I think I get it now, and will include the revised code for review below after testing it. It might make a great example of NSConditionLock, too, so I will probably refactor this page to link it in the NSConditionLock page on this wiki. CharlesParnot

However, I still don't understand what causes to loop to stall. Is it a compiler problem or a runtime problem? I read on this wiki that a thread is put to sleep when cycling for ever on the same instructions. Is that what happens here when i use a       while(threadCount>0) {;}    loop?
----
I don't know where you got the information that a thread is put to sleep when repeatedly cycling on the same instructions; that (as far as I know) is false. The behavior is not a problem, Objective-C is compiled code and it (the processor(s)) will do whatever you tell them to do. I have not taken a detailed look at all of your code; my NSConditionLock suggestions were based on a small project I quickly threw together, loosely based on your code, just as a "sanity check". It's possible that when your main thread enters this tight loop, it consumes enough processor time that your child threads do not get a chance to execute. It could also be something to do with the fact that you are manipulating threadCount outside of a critical section (lock). Considering other threads will be touching this variable, it's not really a good idea.
----

I extrapolated the 'sleep' thing from LiveLock:*...that thread will spin over the same instructions until it is slept automatically...*. Now, I will explain more precisely what my initial problem was, so you don't have to go through the whole code. First, let's make it clear that using an NSConditionLock is the right way to go, but I will talk below of what happens when I go the naive way, which I did initially, and which should still work (with some performance issue, probably). So, here is the naive way. After the main thread is done with its jobs, it is waiting for the other threads to finish. For this, you could simply keep checking threadCount until it reaches 0:

    
while (threadCount>0) {
        //do nothing
        ;
}


I tested with 2 threads (main and secondary). If the main thread finishes first, it will enter that loop. Because of NSLogs, I know that the other thread is still working, finishes, and sets threadCount to 0 (thanks to  '    NSLog(@"threadCount = %d",threadCount);' in the '    decreaseThreadCount' method). However, the loop does not stop and the program never exits!! One thing for sure, here, is that I don't manipulate threadCount. I only look at its value, but if that is what you meant by manipulating, then I will just accept that it could be the problem... I also did the same with a BOOL instead of an int (having another instance variable) and it did not work better. I don't think a      while (flag) {;}  where flag is a BOOL, outside of a lock, is thread-unsafe, is it?

Now if I do instead:
%    
while (threadCount>0) {
		// !!!! It only works if I put this silly code !!!!
		//even though it accesses threadCount
		//it is probably quite safe
		if (threadCount==0)
			threadCount=0;
}


then it behaves properly (even though I am potentially manipulating threadCount this time. I tried to put a number of different instructions in the loop; some work, some don't. It seems I need to include threadCount in it. In the case of the empty loop, maybe the compiler is trying to optimize it without thinking about multithreading: it thinks threadCount will always be the same because I don't touch it in the loop, so the code is set to start an infinite loop if threadCount>0 at the first check and then never checks again. Or is it the runtime, that puts the thread to sleep (that would be low level runtime, like mach? I really don't know what I am talking about here). From what you say, the latter does not happen. So, can somebody come up with an explanation for this purely academic problem?

----

Threads always sleep automatically. Your computer has N different threads running at any given time, and only one processor (or two), so sometimes a running thread has to be forcibly stopped so that another one can start. What instructions a thread is running is irrelevant, as long as it's not making system calls.

Your problem is almost certainly due to optimization. The compiler doesn't know about threads, doesn't think about threads, and doesn't consider threads. It's free to assume that nobody else can change     threadCount. If you want to force it to check the variable every time through the loop, you should declare it with the     volatile keyword.

But in any case, that kind of busy-waiting is evil, so you should use a lock.

*Thanks so much again for your explanations and help. This compiler problem is really all I deserved from not trying to do things right. Anyway, I posted the corrected code (and hopefully correct) as an example of NSConditionLock, in NSConditionLockMultiJobExample.* --CharlesParnot

