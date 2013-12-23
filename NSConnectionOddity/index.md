---
layout: page
title: NSConnectionOddity
---



Does anyone have any insight has to why the following code... 


    
	NSConnection* c1 = [NSConnection connectionWithRegisteredName:@"DoesNotExist" host:nil];
	NSLog(@"c1 = %@", c1);
	
	NSConnection* c2 = [NSConnection defaultConnection];
	[c2 registerName:@"DoesExist"];
	NSLog(@"c2 = %@", c2);
	
	[c2 registerName:@"NewName"];
	[c2 invalidate];
	[c2 release];

	NSConnection* c3 = [NSConnection connectionWithRegisteredName:@"DoesExist" host:nil];
	NSLog(@"c3 = %@", c3);


should produce the following output....

<code>
2007-10-04 01:12:32.530 TestTool[7642] c1 = (null)


2007-10-04 01:12:32.536 TestTool[7642] c2 = (** NSConnection 0x304c40 receivePort <CFMachPort 0x305040 [0xa07bb150]>{locked = No, valid = Yes, port = 0xc0b, source = 0x0, callout = 0x92bd9810, context = <CFMachPort context 0x304f00>} sendPort <CFMachPort 0x305040 [0xa07bb150]>{locked = No, valid = Yes, port = 0xc0b, source = 0x0, callout = 0x92bd9810, context = <CFMachPort context 0x304f00>} refCount 2 **)


2007-10-04 01:12:32.537 TestTool[7642] c3 = (** NSConnection 0x306b70 receivePort <CFMachPort 0x3053b0 [0xa07bb150]>{locked = No, valid = Yes, port = 0x1203, source = 0x0, callout = 0x92bd9810, context = <CFMachPort context 0x305b30>} sendPort <CFMachPort 0x305040 [0xa07bb150]>{locked = No, valid = Yes, port = 0xc0b, source = 0x0, callout = 0x92bd9810, context = <CFMachPort context 0x304f00>} refCount 2 **)
</code>

c1 is obviously what you would expect, but one would think that after at any one of, changing the registered name, invalidating, and releasing, c2 ought to actually be gone, but the fact that c3 exists suggests otherwise.

I managed to work around the problem that something similar to the above was causing, but I'm still curious as to why this is, and if there's any good way around it.

----
My guess would be that it doesn't really get invalidated until you return to the runloop.

