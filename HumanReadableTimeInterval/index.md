---
layout: page
title: HumanReadableTimeInterval
---

Hey there, I've written this method that changes an NSTimeInterval into a human readable NSString. It all works fine and dandy until we try to use it on a NSTimeInterval of a negative value.

Does anyone know of a solution?

Code:
    - (NSString *)secondsToHumanReadableString:(NSTimeInterval)interval
{
	// Due to the inaccuracy of the below constants once you get into scales of 
	// years (something this small tool should never go) time tends to go "faster" 
	// it isn't actually going faster it just seems that way due to rounding errors.
	static const double second = 1;
	static const double minute = second * 60;
	static const double hour = minute * 60;
	static const double day = hour * 12;
	static const double month = day * 30.5; // 30.5 is the average and we don't need to be *THAT* precise.
	static const double year = day * 365; // Years aren't exactly 365 either but it is more precise then ^month * 12.
	
	NSTimeInterval workInterval = interval;
	
	double years = 0;
	double months = 0;
	double days = 0;
	double hours = 0;
	double minutes = 0;
	double seconds = 0;
	
	// Year.
	years = floor(workInterval / year);
	workInterval -= year * years;
	
	months = floor(workInterval / month);
	workInterval -= month * months;
	
	days = floor(workInterval / day);
	workInterval -= day * days;
	
	hours = floor(workInterval / hour);
	workInterval -= hour * hours;
	
	minutes = floor(workInterval / minute);
	workInterval -= minute * minutes;
	
	seconds = floor(workInterval);
	
	NSMutableString * str = [[[NSMutableString alloc] init] autorelease];
	
	if (years == 1) {
		[str appendString:[NSString stringWithFormat:@"%.0lf year ",years]];
	}
	else if (years != 0) {
		[str appendString:[NSString stringWithFormat:@"%.0lf years ",years]];
	}
	
	if (months == 1) {
		[str appendString:[NSString stringWithFormat:@"%.0lf month ",months]];
	}
	else if (months != 0) {
		[str appendString:[NSString stringWithFormat:@"%.0lf months ",months]];
	}
	
	if (days == 1) {
		[str appendString:[NSString stringWithFormat:@"%.0lf day ",days]];
	}
	else if (days != 0) {
		[str appendString:[NSString stringWithFormat:@"%.0lf days ",days]];
	}
	
	if (hours == 1) {
		[str appendString:[NSString stringWithFormat:@"%.0lf hour ",hours]];
	}
	else if (hours != 0) {
		[str appendString:[NSString stringWithFormat:@"%.0lf hours ",hours]];
	}
	
	if (minutes == 1) {
		[str appendString:[NSString stringWithFormat:@"%.0lf minute ",minutes]];
	}
	else if (minutes != 0) {
		[str appendString:[NSString stringWithFormat:@"%.0lf minutes ",minutes]];
	}
	
	if (seconds == 1) {
		[str appendString:[NSString stringWithFormat:@"%.0lf second ",seconds]];
	}
	else if (seconds != 0) {
		[str appendString:[NSString stringWithFormat:@"%.0lf seconds ",seconds]];
	}
	
	if ([str isEqualToString:@""]) [str setString:@"0 seconds "];
	return str;
}----

Looks pretty nice. You can solve your negative problem by checking for negative at the beginning, possibly adding a string or flag, and then inverting the value. Something like:

    
if(workInterval < 0.0)
{
   [str appendString:@"Negative "];
   workInterval = -workInterval;
}


On a style/efficiency point, you can replace all of your lines that have     [str appendString:[NSString stringWithFormat:...]] with the simpler and less wasteful     [str appendFormat:...].

----

(extremely minor nitpick) You might also want to change the name of the method to -stringForTimeInterval: to match Cocoa's... (I'd place it into a category of NSString, personally, and make it a class method).

----

You should check out NSCalendarDate. There are some methods that provide string descriptions of time intervals based on a user format. You have to create an NSCalendarDate object first from a time interval, but the rest is in the docs. --zootbobbalu

---- 

Thanks for the solutions all :-). I agree the name of the method wasn't the best (hell it annoyed me as well!) but it was 3am.....

----

I created a version of this which mimics the time_ago_in_words helper from rails.

    
#import "NSString+TimeInterval.h"

#import <math.h>

@implementation NSString (TimeInterval)

+ (NSString *)stringForTimeInterval:(NSTimeInterval)interval includeSeconds:(BOOL)includeSeconds
{
  NSTimeInterval intervalInSeconds = fabs(interval);
  double intervalInMinutes = round(intervalInSeconds/60.0);
  
  if (intervalInMinutes >= 0 && intervalInMinutes <= 1)
  {
    if (!includeSeconds) return intervalInMinutes == 0 ? @"less than a minute" : @"1 minute";
    if (intervalInSeconds >= 0 && intervalInSeconds <= 4) return @"less than 5 seconds";
    else if (intervalInSeconds >= 5 && intervalInSeconds <= 9) return @"less than 10 seconds";
    else if (intervalInSeconds >= 10 && intervalInSeconds <= 19) return @"less than 20 seconds";
    else if (intervalInSeconds >= 20 && intervalInSeconds <= 39) return @"half a minute";
    else if (intervalInSeconds >= 40 && intervalInSeconds <= 59) return @"less than a minute";
    else return @"1 minute";
  }
  else if (intervalInMinutes >= 2 && intervalInMinutes <= 44) return [NSString stringWithFormat:@"%.0f minutes", intervalInMinutes];
  else if (intervalInMinutes >= 45 && intervalInMinutes <= 89) return @"about 1 hour";
  else if (intervalInMinutes >= 90 && intervalInMinutes <= 1439) return [NSString stringWithFormat:@"about %.0f hours", round(intervalInMinutes/60.0)];
  else if (intervalInMinutes >= 1440 && intervalInMinutes <= 2879) return @"1 day";
  else if (intervalInMinutes >= 2880 && intervalInMinutes <= 43199) return [NSString stringWithFormat:@"%.0f days", round(intervalInMinutes/1440.0)];
  else if (intervalInMinutes >= 43200 && intervalInMinutes <= 86399) return @"about 1 month";
  else if (intervalInMinutes >= 86400 && intervalInMinutes <= 525599) return [NSString stringWithFormat:@"%.0f months", round(intervalInMinutes/43200.0)];
  else if (intervalInMinutes >= 525600 && intervalInMinutes <= 1051199) return @"about 1 year";
  else
    return [NSString stringWithFormat:@"over %.0f years", round(intervalInMinutes/525600.0)];    
}

@end


----

Of course, you'd want to be able to localize that...

----

One more thing - day has 24 hours, not 12 ,)

----
For negative intervals, how about appending 'ago' ?

