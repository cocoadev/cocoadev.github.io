---
layout: page
title: HowToRoundOffNSCalendarDate
---

Hi,

is there any way I can get an NSCalendarDate to round itself to the nearest 5 minutes?

If not, can anyone point me in the right direction on how to achieve this manually?

Thanks for your help!

-P

----
**Interface**

    
@interface NSDate (MyAdditions)

- (NSDate*)roundedToNearestFiveMinuteMark;

@end


**Implementation**

    
@implementation NSDate (MyAdditions)

- (NSDate*)roundedToNearestFiveMinuteMark
{
	NSTimeInterval interval = [self timeIntervalSinceReferenceDate];
	long int lquo = lrint(interval / 300.0);
	NSTimeInterval seconds = (lrint(remainder(interval, 300.0)) < 150) ? (lquo * 300) : ((lquo + 1) * 300);
	return [NSDate dateWithTimeIntervalSinceReferenceDate:seconds];
}

@end

