---
layout: page
title: DaysInMonthHow
---

Hi,

How could I get number of days in any month?

Thank you


----


    

static int DaysInMonth(int month, int year) {
    NSTimeZone *EST = [NSTimeZone timeZoneWithAbbreviation:@"EST"];
    NSCalendarDate *day27 = [NSCalendarDate dateWithYear:year month:month day:27 hour:12 minute:0 second:0 timeZone:EST];
    NSCalendarDate *day27Plus5Days = [day27 dateByAddingYears:0 months:0 days:5 hours:0 minutes:0 seconds:0];
    return 32 - [day27Plus5Days dayOfMonth];
}



There has to be an easier way!!

--zootbobbalu

----

This sounds like it would be easier to do manually:
    
static int monthDaysTable[12] = { 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };
int daysInMonth(int month, int year)
{
	NSAssert(month >= 1 && month <= 12, @"month is invalid");
	// leap years occur when the year is divisible by 4 but not 100, unless it's divisible by 400 in which case it is again
	if (month == 2 && (year % 4) == 0 && ((year % 100) != 0 || (year % 400) == 0)) {
		return 29;
	} else {
		return monthDaysTable[month-1];  // gotta subtract 1 to switch to 0-based index.
	}
}

I'm pretty sure that's the right algorithm but I didn't look it up to be totally sure. -- Bo

----

Like the mathematical approach, but what if an asteroid shifts the earth's orbit and leap years have to be redefined. Wouldn't it be better to rely on some system level API ;-)

--zootbobbalu

----

Fair enough. I always thought the Egyptian method of having 360 days in a year, and just throwing a big party for the extra 5 or 6 days was the way to go.  -- Bo

----

I could go for the party idea, How about Just forgetting the years and months altogether and just count rotations.
    
.
.
.
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(solarOrbitComplete:) name:NSSolarOrbitComplete object:[HomeWorld defaultInstance]];
.
.
.
- (void)solarOrbitComplete:(NSNotification*)aNotification
{
	@synchronized (self) {
		++m_day;
	}
}

