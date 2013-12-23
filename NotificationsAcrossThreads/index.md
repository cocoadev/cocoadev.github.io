---
layout: page
title: NotificationsAcrossThreads
---





Cocoa uses Notifications as a convenient way to broadcast one message to multiple Clients. See NSNotification, NSNotificationCenter, and NotificationSampleCode for more information on using notifications. 

When your application posts a notification with NSNotificationCenter your app will block until all of the registered notification observers have processed your notification. This is very convenient -- observers receive notifications immediately and posters can continue knowing all interested parties have processed their requests. (Note that the NSDistributedNotificationCenter does not behave this way -- I'll save that for another time)

But this poses a problem for multi-threaded apps. If an app posts a notification from a secondary thread, the observers will receive the notification on that secondary thread. Often, as is the case in the NotificationSampleCode example, it isn't obvious that that's the case. The secondary thread is a tight method with a clear beginning, middle, and end.

This is a problem because much of AppKit isn't thread-safe -- unless you know otherwise, AppKit operations should be performed on the main thread only. Most of Carbon (which provides the implementation for a number of AppKit functions, such as the menu bar) also is not thread-safe -- all Carbon operations should be performed on the main thread only. And OpenGL (to bring up another example) is thread-safe only if you can guarantee its only being called on one thread (not necessarily the main thread) -- I call OpenGL "Thread Agnostic". App writers need to be very conscious of the work they are doing on secondary threads and ensure only safe functions and methods are used at this time.

Here's a sample program that illustrates the problem:

    
#include <unistd.h>
#import <Foundation/Foundation.h>

static NSString *ThreadTestDidFinishWork = @"ThreadTestDidFinishWork";
static BOOL gDone = false; // how old fashioned ... 

@interface ThreadTest : NSObject
{
}
- (void)startWork;
- (void)doWork:(id)sender;
- (void)finishWorkHandler:(NSNotification*)note;
@end

@implementation ThreadTest

- (void)startWork
{
    // register our handler
    [[NSNotificationCenter defaultCenter] addObserver:self 
selector:@selector(finishWorkHandler:) name:ThreadTestDidFinishWork
object:nil];
    
    // start our thread
    [NSThread detachNewThreadSelector:@selector(doWork:) toTarget:self withObject:nil];

}

- (void)doWork:(id)sender
{
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    NSLog(@"work thread: %@", [NSThread currentThread]);

    // do time intensive work
    sleep(5);
    
    // post notification
    [[NSNotificationCenter defaultCenter] 
postNotificationName:ThreadTestDidFinishWork object:nil];
    
    [pool release];
}

- (void)finishWorkHandler:(NSNotification*)note
{
    NSLog(@"note thread: %@", [NSThread currentThread]);

    // unregister
    [[NSNotificationCenter defaultCenter] removeObserver:self];
    
    // quit
    gDone = true;
}

@end

int main (int argc, const char * argv[])
{
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
    ThreadTest *test = [[ThreadTest alloc] init];

    NSLog(@"main thread: %@", [NSThread currentThread]);

    [test startWork];

    // please don't write real apps like this! this is just for the
    // sake of the test! use real run loops! I beg you!
    while (!gDone) {
        (void)[[NSRunLoop currentRunLoop] 
runMode:NSDefaultRunLoopMode      beforeDate:[NSDate distantFuture]];
    }
    
    [test release];

    [pool release];
    return 0;
}


When the above program is compiled and run it yields the following output:

    
[localhost:Shared/Builds/mtrent] mike% ./threadNoteTest
Apr 25 00:57:03 threadNoteTest[384] main thread: Thread 0x4c200
Apr 25 00:57:03 threadNoteTest[384] work thread: Thread 0x5f940
Apr 25 00:57:09 threadNoteTest[384] note thread: Thread 0x5f940


So it's clear our notification was posted and received on our worker thread.

Remember I said notification observers receive requests immediately and that posters block waiting for them to finish? We can verify this easily in gdb:

    
[localhost:Shared/Builds/mtrent] mike% gdb ./threadNoteTest
GNU gdb 5.0-20001113 (Apple version gdb-186.1) (Sun Feb 18 01:18:32 GMT 2001) (UI_OUT)
Copyright 2000 Free Software Foundation, Inc.
GDB is free software, covered by the GNU General Public License, and you are
welcome to change it and/or distribute copies of it under certain conditions.
Type "show copying" to see the conditions.
There is absolutely no warranty for GDB.  Type "show warranty" for details.
This GDB was configured as "powerpc-apple-macos10".
Reading symbols for shared libraries ... done
(gdb) br finishWorkHandler:
Breakpoint 1 at 0x29e0: file main.m, line 43.
(gdb) r
Starting program: /Users/Shared/Builds/mtrent/./threadNoteTest 
[Switching to thread 1 (process 393 thread 0x1903)]
Reading symbols for shared libraries ... done
Error in re-setting breakpoint 1:
Function "main.m:-[ThreadTest finishWorkHandler:]" not defined.
Re-enabling shared library breakpoints: 1
Apr 25 01:02:47 threadNoteTest[393] main thread: Thread 0xc200
Apr 25 01:02:47 threadNoteTest[393] work thread: Thread 0x1f930
[Switching to thread 2 (process 393 thread 0x2403)]
[Switching to process 393 thread 0x2403]

Breakpoint 1, -[ThreadTest finishWorkHandler:] (self=0xe000, _cmd=0x2dd8, note=0x21140)
at main.m:43
43          NSLog(@"note thread: %@", [NSThread currentThread]);
(gdb) t a a bt

Thread 2 (process 393 thread 0x2403):
#0  -[ThreadTest finishWorkHandler:] (self=0xe000, _cmd=0x2dd8, note=0x21140) at main.m:43
#1  0x70989598 in _nsNotificationCenterCallBack ()
#2  0x70198e20 in _postNotification ()
#3  0x70196750 in _CFNotificationCenterPostLocalNotification ()
#4  0x70196494 in _CFNotificationCenterPostNotification ()
#5  0x7097fa5c in -[NSNotificationCenter postNotificationName:object:userInfo:flags:] ()
#6  0x7097f9ec in -[NSNotificationCenter postNotificationName:object:] ()
#7  0x0000298c in -[ThreadTest doWork:] (self=0xe000, _cmd=0x2dec, sender=0x0) at main.m:36
#8  0x709ecc34 in forkThreadForFunction ()
#9  0x700150f4 in _pthread_body ()
#10 0x00000000 in ?? ()

Thread 1 (process 393 thread 0x1903):
#0  0x7099a544 in -[NSRunLoop runMode:beforeDate:] ()
#1  0x00002c30 in main (argc=1, argv=0xbffffb38) at main.m:65
#2  0x000026fc in _start ()
#3  0x0000253c in start ()
#4  0x00000000 in ?? ()
43          NSLog(@"note thread: %@", [NSThread currentThread]);
(gdb) 


Note that Thread 1 is still spinning along processing our runloop (I'll bet you were wondering why I bothered with that! I wanted to show it doing something in gdb). Thread 2 is sitting in our notification handler. We can clearly see the program execution in doWork: posts the notification (frame 7) and our observer received the notification directly (frame 0).

Knowing that notifications are posted immediately to observers is good to know. It means observers can assume they will receive their notifications immediately and posters can assume all observers have completed processing their notifications before resuming. But that means program authors need to be extra careful when posting notifications from worker threads, since many of our libraries and classes are not thread-safe.

-- MikeTrent

----

See also: ThreadSafety ThreadWorker ThreadCommunication InterThreadMessaging

----

Here is some GPL code I wrote that lets you easily send notifications to the main thread (with the option to wait or not).

    
@interface NSNotificationCenter (NSNotificationCenterAdditions)
- (void) postNotificationOnMainThread:(NSNotification *) notification;
- (void) postNotificationOnMainThread:(NSNotification *) notification waitUntilDone:(BOOL) wait;

- (void) postNotificationOnMainThreadWithName:(NSString *) name object:(id) object;
- (void) postNotificationOnMainThreadWithName:(NSString *) name object:(id) object userInfo:(NSDictionary *) userInfo;
- (void) postNotificationOnMainThreadWithName:(NSString *) name object:(id) object userInfo:(NSDictionary *) userInfo waitUntilDone:(BOOL) wait;
@end

@interface NSNotificationQueue (NSNotificationQueueAdditions)
- (void) enqueueNotificationOnMainThread:(NSNotification *) notification postingStyle:(NSPostingStyle) postingStyle;
- (void) enqueueNotificationOnMainThread:(NSNotification *) notification postingStyle:(NSPostingStyle) postingStyle coalesceMask:(unsigned) coalesceMask forModes:(NSArray *) modes;
@end


    
#import "NSNotificationAdditions.h"
#import <pthread.h>

@implementation NSNotificationCenter (NSNotificationCenterAdditions)
- (void) postNotificationOnMainThread:(NSNotification *) notification {
	if( pthread_main_np() ) return [self postNotification:notification];
	[self postNotificationOnMainThread:notification waitUntilDone:NO];
}

- (void) postNotificationOnMainThread:(NSNotification *) notification waitUntilDone:(BOOL) wait {
	if( pthread_main_np() ) return [self postNotification:notification];
	self class] performSelectorOnMainThread:@selector( _postNotification: ) withObject:notification waitUntilDone:wait];
}

+ (void) _postNotification:([[NSNotification *) notification {
	self defaultCenter] postNotification:notification];
}

- (void) postNotificationOnMainThreadWithName:([[NSString *) name object:(id) object {
	if( pthread_main_np() ) return [self postNotificationName:name object:object userInfo:nil];
	[self postNotificationOnMainThreadWithName:name object:object userInfo:nil waitUntilDone:NO];
}

- (void) postNotificationOnMainThreadWithName:(NSString *) name object:(id) object userInfo:(NSDictionary *) userInfo {
	if( pthread_main_np() ) return [self postNotificationName:name object:object userInfo:userInfo];
	[self postNotificationOnMainThreadWithName:name object:object userInfo:nil waitUntilDone:NO];
}

- (void) postNotificationOnMainThreadWithName:(NSString *) name object:(id) object userInfo:(NSDictionary *) userInfo waitUntilDone:(BOOL) wait {
	if( pthread_main_np() ) return [self postNotificationName:name object:object userInfo:userInfo];

	NSMutableDictionary *info = [[NSMutableDictionary allocWithZone:nil] init];
	[info setObject:name forKey:@"name"];
	if( object ) [info setObject:object forKey:@"object"];
	if( userInfo ) [info setObject:userInfo forKey:@"userInfo"];

	self class] performSelectorOnMainThread:@selector( _postNotificationName: ) withObject:info waitUntilDone:wait];
	[info release];
}

+ (void) _postNotificationName:([[NSDictionary *) info {
	NSString *name = [info objectForKey:@"name"];
	id object = [info objectForKey:@"object"];
	NSDictionary *userInfo = [info objectForKey:@"userInfo"];

	self defaultCenter] postNotificationName:name object:object userInfo:userInfo];
}
@end

@implementation [[NSNotificationQueue (NSNotificationQueueAdditions)
- (void) enqueueNotificationOnMainThread:(NSNotification *) notification postingStyle:(NSPostingStyle) postingStyle {
	if( pthread_main_np() ) return [self enqueueNotification:notification postingStyle:postingStyle coalesceMask:( NSNotificationCoalescingOnName | NSNotificationCoalescingOnSender ) forModes:nil];
	[self enqueueNotificationOnMainThread:notification postingStyle:postingStyle coalesceMask:( NSNotificationCoalescingOnName | NSNotificationCoalescingOnSender ) forModes:nil];
}

- (void) enqueueNotificationOnMainThread:(NSNotification *) notification postingStyle:(NSPostingStyle) postingStyle coalesceMask:(unsigned) coalesceMask forModes:(NSArray *) modes {
	if( pthread_main_np() ) return [self enqueueNotification:notification postingStyle:postingStyle coalesceMask:coalesceMask forModes:modes];

	NSMutableDictionary *info = [[NSMutableDictionary allocWithZone:nil] init];
	[info setObject:notification forKey:@"notification"];
	[info setObject:[NSNumber numberWithUnsignedInt:postingStyle] forKey:@"postingStyle"];
	[info setObject:[NSNumber numberWithUnsignedInt:coalesceMask] forKey:@"coalesceMask"];
	if( modes ) [info setObject:modes forKey:@"modes"];

	self class] performSelectorOnMainThread:@selector( _enqueueNotification: ) withObject:info waitUntilDone:NO];
	[info release];
}

+ (void) _enqueueNotification:([[NSDictionary *) info {
	NSNotification *notification = [info objectForKey:@"notification"];
	NSPostingStyle postingStyle = info objectForKey:@"postingStyle"] unsignedIntValue];
	unsigned coalesceMask = [[info objectForKey:@"coalesceMask"] unsignedIntValue];
	[[NSArray *modes = [info objectForKey:@"modes"];

	self defaultQueue] enqueueNotification:notification postingStyle:postingStyle coalesceMask:coalesceMask forModes:modes];
}
@end


Latest version of the code available at: http://project.colloquy.info/trac/browser/trunk/Additions/[[NSNotificationAdditions.m

~ TimothyHatcher

----

Thanks a lot - This is very helpful.

There is a typo in one of your method - You force the userInfo to be nil.
    
- (void) postNotificationOnMainThreadWithName:(NSString *) name object:(id) object userInfo:(NSDictionary *) userInfo {
	if( pthread_main_np() ) return [self postNotificationName:name object:object userInfo:userInfo];
	[self postNotificationOnMainThreadWithName:name object:object userInfo:nil waitUntilDone:NO];
}


should be:
    
- (void) postNotificationOnMainThreadWithName:(NSString *) name object:(id) object userInfo:(NSDictionary *) userInfo {
	if( pthread_main_np() ) return [self postNotificationName:name object:object userInfo:userInfo];
	[self postNotificationOnMainThreadWithName:name object:object userInfo:userInfo waitUntilDone:NO];
}


Also, on iPhone, it's easier (and probably safer) to replace:
    
if ( pthread_main_np() ) {
}


with:
    
if ([NSThread isMainThread]) {
}


I dont know if this would work with Cocoa on MacOS.

~ Thomas Sarlandie

