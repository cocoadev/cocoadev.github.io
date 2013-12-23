---
layout: page
title: CountDownTimer
---

I want to make a program that accurately counts down to a certain date specified by the user and shows the days, minutes, hours, and seconds until the day the countdown is supposed to end. Would the best way to do this be to use an NSTimer and have it fire every one second and then subtract the future date and time from the current date and time?

**
Use (NSTimeInterval)timeIntervalSinceDate:(NSDate *)anotherDate, where anotherDate is the date of the event of your choice. Convert the result into a NSDate object and format it so you can see the seconds scroll down. Note that your timeInterval may be a negative number...
**

----

Here's an NSString category for converting seconds to either "X seconds/minutes/hours/days" or "HH:MM:SS" format:
    
+ (NSString *)formatSeconds:(unsigned long long)seconds withWords:(BOOL)withWords
{
	float second = 1.0;
	float minute = second * 60;
	float hour = minute * 60;
	float day = hour * 24;

	if (withWords == YES)
	{
		if (seconds < minute)
			return [NSString stringWithFormat:@"%d seconds", seconds];
		else if (seconds < hour)
			return [NSString stringWithFormat:@"%.1f minutes", seconds / minute];
		else if (seconds < day)
			return [NSString stringWithFormat:@"%.1f hours", seconds / hour];
		else
			return [NSString stringWithFormat:@"%.1f days", seconds / day];
	}
	else
	{
		if (seconds < minute)
		{
			return [NSString stringWithFormat:@"0:%02d", (long)seconds];
		}
		else if (seconds < hour)
		{
			return [NSString stringWithFormat:@"%d:%02d", (long)seconds/60, (long)seconds%60];
		}
		else// if (seconds < day)
		{
			return [NSString stringWithFormat:@"%d:%02d:%02d", (long)seconds/(60*60), (long)seconds/60%60, (long)seconds%60];
		}
	}
	
	return nil;
}

*This would be better implemented as an NSFormatter -- perhaps even an NSDateFormatter? --JediKnil*

