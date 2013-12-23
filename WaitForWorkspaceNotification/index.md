---
layout: page
title: WaitForWorkspaceNotification
---

Hi. I am trying to create a foundation tool that records workspace notifications (using General/NSLog()).

I am registering for notifications using:

General/NSNotificationCenter *center = General/[[NSWorkspace sharedWorkspace] notificationCenter]; 
[center addObserver:self selector: @selector(update:) name:nil object:nil];

This all works fine when the application stays alive (i.e. if I had a full blown General/NSApplication etc...). However, I am currently usure how to keep my "foundation tool" alive. I have looked into threads, run loops etc... but no topics seem to directly or indirectly apply. 

Any help would be gratefully appreciated.

----

    
#import <Foundation/Foundation.h>

@interface General/MainObject : General/NSObject {

}
@end

@implementation General/MainObject
- (void)globalNotification:(General/NSNotification *)note {

	id object = [note object];	
	General/NSString *date = General/[[NSCalendarDate date] descriptionWithCalendarFormat:@"%A %B %d, %Y %i:%M:%S.%F %p"];
	General/NSString *log = General/[NSString stringWithFormat:@"\
<<<<<<<<<<  %@  >>>>>>>>>>\n\
Time: %f\n\
Notification: %@\n\
Object Class: %@\n\
Object: %@\n\
User Info: %@\n",
			date, 
			General/CFAbsoluteTimeGetCurrent(),
			[note name],
			General/NSStringFromClass([object class]),
			object,
			[note userInfo]];
			
	printf([log UTF8String]);
}

- (void)start:(General/NSPort *)port {
	static BOOL didRegister = NO;
	if (!didRegister) {
		General/[[NSDistributedNotificationCenter defaultCenter] addObserver:self
															selector:@selector(globalNotification:)
																name:nil
															  object:nil];
		didRegister = YES;
	}
	General/NSLog(@"<%p>%s: port: %@", self, __PRETTY_FUNCTION__, [port description]);
}
@end

int main(int argc, char *argv[]) {
	General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];
	General/NSRunLoop *loop = General/[NSRunLoop currentRunLoop];
	General/MainObject *mainObject = General/[[MainObject alloc] init];
	General/NSPort *port = General/[NSPort port];
	[loop addPort:port forMode:General/NSDefaultRunLoopMode];
	[loop configureAsServer];
	[loop performSelector:@selector(start:) 
				   target:mainObject 
				 argument:port 
					order:nil 
					modes:General/[NSArray arrayWithObject:General/NSDefaultRunLoopMode]];
	[loop run];
	[pool release];
	[mainObject release];
	return 0;
}


--zootbobbalu

----

Thank you very much. My understanding of such matters is currently "very low" so please bare with me! 

I am particularly interested in application opening and closing notices from the General/NSWorkspace notification center. However, when I replace General/[[NSDistributedNotificationCenter defaultCenter] addObserver... code with:     General/[[[NSWorkspace sharedWorkspace] notificationCenter] addObserver:self selector:@selector(globalNotification:) name:nil object:nil];I receive no notifications. If I leave the code alone, I receive notifications but not the ones I am interested in.

Thanks for any further help!

----

The code above was just a starting point. If General/NSWorkspace's notification center provides you with the info you need then use that instead of DNC.

--zootbobbalu

----

Yes. The problem is that I cannot recieve notifications from the General/NSWorkspace notification center. I have included the General/AppKit framework and called General/NSApplicationLoad(), so General/[NSWorkspace sharedWorkspace] is available (I can see a list of running apps using one of its methods), however no notifications are being received. I am truly at a loss why this is the case. Could it be because I am not calling General/NSApplicationMain()? If so, I cannot see a way round the problem. Thanks again.

----

Try using     General/[NSApp run] rather than     [loop run].

----

Thanks! I shall go and learn something about run loops I think.

----

It's not clear to me why you invoked "configureAsServer". According to the General/NSRunLoop docs, "On Mac OS X, this method does nothing."
