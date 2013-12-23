---
layout: page
title: InterThreadMessaging
---

**Download** source code at http://files.me.com/d_j_v/3trcvx

See ThreadCommunication ThreadSafety for other techniques.

----

InterThreadMessaging, Created by Toby Paterson 19 June 2001

A low-cost-but-not-quite-as-general alternative to D.O.

This is a category on NSThread, NSNotificationCenter, and NSObject. The inter-thread messaging category methods use NSPort**'s to deliver messages between threads.  In order to receive an inter-thread message, the receiver must both (1) be running a run loop; and (2) be monitoring this port in its run loop. A method on NSThread, to be called from each thread that will participate, sets this up.

The category offers the following methods:

*+ (void) prepareForInterThreadMessages;
*- (void) performSelector:(SEL)selector inThread:(NSThread *)thread;
*- (void) performSelector:(SEL)selector inThread:(NSThread *)thread beforeDate:(NSDate *)limitDate;
*- (void) performSelector:(SEL)selector withObject:(id)object inThread:(NSThread *)thread;
*- (void) performSelector:(SEL)selector withObject:(id)object inThread:(NSThread *)thread beforeDate:(NSDate *)limitDate;
*- (void) performSelector:(SEL)selector withObject:(id)object1 withObject:(id)object2 inThread:(NSThread *)thread;
*- (void) performSelector:(SEL)selector withObject:(id)object1 withObject:(id)object2 inThread:(NSThread *)thread beforeDate:(NSDate *)limitDate;
*- (void) postNotification:(NSNotification *)notification inThread:(NSThread *)thread;
*- (void) postNotification:(NSNotification *)notification inThread:(NSThread *)thread beforeDate:(NSDate *)limitDate;
*- (void) postNotificationName:(NSString *)name object:(id)object inThread:(NSThread *)thread;
*- (void) postNotificationName:(NSString *)name object:(id)object inThread:(NSThread *)thread beforeDate:(NSDate *)limitDate;
*- (void) postNotificationName:(NSString *)name object:(id)object userInfo:(NSDictionary *)userInfo inThread:(NSThread *)thread;
*- (void) postNotificationName:(NSString *)name object:(id)object userInfo:(NSDictionary *)userInfo inThread:(NSThread *)thread beforeDate:(NSDate *)limitDate;


----

The author's original e-mail:
    
From: Toby Paterson <tpater@mac.com>
Subject: Inter-thread messaging: a solution

The topic of how to communicate between two threads efficiently has come 
up a number of times while I've been on this list.  I've written some 
code to facilitate this and am making it available for your use and 
abuse.

The model expects that you have one or more worker threads that need to 
post information back to the main, UI thread, though it can be used to 
communicate with any thread running a run loop.  It takes the form of 
categories on NSObject and NSNotificationCenter to allow you to invoke 
methods and post notifications in a specific thread:

     [master performSelector:@selector(workerDone:)
             withObject:self
             inThread:mainThread];

     [[NSNotificationCenter defaultCenter]
         postNotificationName:kSomethingHappened
         object:self
         inThread:mainThread
         beforeDate:[NSDate distantFuture]];

The implementation uses NSPortMessages to marshall the message to the 
target thread.  It's more light-weight than DO - NSPortMessage and 
NSPort are a fairly thin veneer on top of mach messaging - but not 
nearly as general.  Make sure you read the comments in the header for 
some caveats of the argument marshalling.

You can download it from:   
http://homepage.mac.com/tpater/InterThreadMessaging.dmg
I'm releasing this code in to the public domain: use it; modify it; ship 
it in free or commercial software; just don't expect any support nor 
assign to me any blame if using this code causes you any mental or 
physical harm.  Have fun.


----

It is possible to deadlock this library if thread A posts a notification on thread B, and the notification on B causes a selector or notification to be posted on thread A. Possibly under other circumstances. I have resolved this in my own code by creating an inter-thread communication lock. When a thread wants to communicate, it tries the lock to see if another thread is already using the InterThreadMessaging library, and if it can't get the lock, it posts a message to its own run-loop to try again later. This is not a *good* solution, but it seems to work well enough. -- DustinVoss

----

[Moved another technique to ThreadCommunication]

----

Please note that this code uses SPI in NSThread that isn't guaranteed to be present or return anything useful or return anything useful at a particular time and as such may cause your software to break on other releases of Mac OS X.

In particular, the threadDied: method on InterThreadManager makes a call for a thread's run loop when the code it's going to call doesn't even need the run loop to do its work.

