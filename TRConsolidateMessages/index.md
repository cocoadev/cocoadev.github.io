---
layout: page
title: TRConsolidateMessages
---

General/TRConsolidateMessages is a small class I wrote to consolidate messages for myself, it was inspired by General/NSNotificationQueue.

Unlike General/NSNotificationQueue I didn't want to mess around with General/NSNotificationCenter for this code which must be called often yet is relatively slow (redrawing).

I am releasing this code to the everyone under the BSD Licence so that everyone may benefit.

**Upsides:**

* Only one message instead of many is sent, speeding up your interface.


**Downsides**

* Messages are sent in the next run loop.


**Usage:**
    #import "Controller.h"
#import "General/TRConsolidatingObject.h"

@implementation Controller
- test:(id)sender
{
	General/TRScheduleMessage(@selector(cpuhog),self,nil);
	General/TRScheduleMessage(@selector(cpuhog),self,nil);
	General/TRScheduleMessage(@selector(cpuhog),self,nil);
	General/TRScheduleMessage(@selector(cpuhog),self,nil);
	
	General/TRScheduleMessage(@selector(cpuhogWithObject:),self,General/[NSArray arrayWithObject:@"blah!"]);
	General/TRScheduleMessage(@selector(cpuhogWithObject:),self,General/[NSArray arrayWithObject:@"blah!"]);
	General/TRScheduleMessage(@selector(cpuhogWithObject:),self,General/[NSArray arrayWithObject:@"weee!"]);
	General/TRScheduleMessage(@selector(cpuhogWithObject:),self,General/[NSArray arrayWithObject:@"blah!"]);
	return self;
}

- cpuhog
{
	General/NSLog(@"cpu limiting function called");
	return self;
}

- cpuhogWithObject:(General/NSArray *)array
{
	General/NSLog(@"%@",array);
	return self;
}

@end


You may also use the objC method:     General/[[TRConsolidatingObject consolidator] scheduleMessage:selector to:target withObject:object]; However the function is easier to type.

**Download:**
http://triage-software.com/leach?id=General/TRConsolidateMessages

**Comments:**
----
