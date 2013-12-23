---
layout: page
title: GetTheDateForCertainDayOfTheWeek
---

What would be the best way to get the date for a certain day of the week? In other words, if I want to calculate the date for the next Monday, how should I do this?
Thanks,

-Eamon

----

    
- (NSDate *)nextMonday {
	NSCalendarDate *today = [NSCalendarDate date];
	int todayDayOfWeek = [today dayOfWeek];
       int daysToAdd = todayDayOfWeek == 0 ? 1 : 7-todayDayOfWeek+1;
	NSCalendarDate *nextMonday = [today dateByAddingYears:0 months:0 days:daysToAdd hours:0 minutes:0 seconds:0];
	return nextMonday;
}


Notice that dayOfWeek==0 means Sunday.

-- DenisGryzlov

----
Aha, thanks. I feel really stupid. :p

-Eamon

