---
layout: page
title: HoursMinutesSeconds
---



Part of a program that I'm working on calculates an amount of time in seconds. I need to know what the best way is to calculate and format the time in Hours:Minutes:Seconds (00:00:00) format. http://goo.gl/OeSCu

-- JacobHazelgrove

----

Maybe you could do something like:

NSCalendarDate *date=[NSCalendarDate dateWithTimeIntervalSince1970:numberOfSeconds];

and then use a NSCalendarDate method on date such as 

-years:months:days:hours:minutes:seconds:sinceDate: (with the last parameter being [NSDate dateWithTimeIntervalSince1970:0])

or

-descriptionWithCalendarFormat:

----

I am not sure if this is the answer to your question, but would it not be easier to use a NSDateFormatter on an interface object or simply splice together a string [NSString stringByAppendingString:@""] if needed?

-- MatPeterson

----

I got a similar problem, and instead of trying to include formatters (which may be a good solution), I simply created a formatted string of my own :

    
    int h,m,s;           // Stand for hours, minutes, seconds
    [theField setStringValue:[NSString stringWithFormat:@"%i : %i : %i", h, m, s]];


I'm sure there are some other ways to do it, but this one is quite easy. Note that in my piece of programming, I didn't think about getting the values to calculate them or make a NSDate with them. If it's what you want to do, this might be a little more complicated...

-- Trax

----

Yeah my main problem is taking seconds and computing it into hours, minutes, and seconds. For now I'm just going to have three fields to output the time: 

    
    [theHoursField setIntValue:hours];
    [theMinutesField setIntValue:minutes];
    [theSecondsField setIntValue:seconds];


-- JacobHazelgrove

----

With the modulo operator (%), it's fairly simple to get hours and minutes from the total amount of seconds. Assuming you have a variable called totalSeconds, for example :

    
long totalSeconds;

hours = (totalSeconds / 3600);               // returns number of whole hours fitted in totalSecs
minutes = ((totalSeconds / 60) - hours*60);  // Whole minutes

// Here we can use modulo to get num secs NOT fitting in whole minutes (60 secs)
seconds = (totalSeconds % 60)

[theField setStringValue:[NSString stringWithFormat:@"%i : %i : %i", hours, minutes, seconds]];


-- Trax  (Hmm... I'm not sure I like this, Trax; you rewrote the text above the code, but it was I that wrote the example code, and originally, there was a "-- EnglaBenny" note here! -- EnglaBenny)

----

Thanks guys, I had to re-work a bit of my other code for it to work, but it works great.

-- JacobHazelgrove

----

What about leap seconds though?

----

For my purpose, leap seconds will never come into play. The app deals mostly with hours and anything less, and so being accurate out to years, would be major overkill.

- JacobHazelgrove

----

We're actually overdue for another leap-second. You might want to consider it. Maybe. -- DustinVoss

----

I am using it for an app that figures roughly how long it will take to download a file. Now unless you are downloading a few hundred terabytes at 2kb/sec, there is no need for leap-seconds.

- JacobHazelgrove

----

You're clearly not pedantic enough to be a RealProgrammer ;) (Ever read RealProgrammersDontUsePascal? It's in the jargon file, I believe, and is quite amusing.) -- RobRix

----
I did a little test and a few hundred (300) terabytes at 2kb/sec would come out to (APPROXIMATELY) 64 years, 240 days, 5 hours, 14 minutes, and 7 seconds. Not including leap seconds.

-- JacobHazelgrove

----

And you don't want the increased precision in your download-figuring program? Psh! :) -- RobRix

----
There was a slight error (16282 years) in the figures above. Fixed now.

-- JacobHazelgrove

----

Ya'll provided this here code above:


    
long totalSeconds;

hours = (totalSeconds / 3600);               // returns number of whole hours fitted in totalSecs
minutes = ((totalSeconds / 60) - hours*60);  // Whole minutes

// Here we can use modulo to get num secs NOT fitting in whole minutes (60 secs)
seconds = (totalSeconds % 60)

[theField setStringValue:[NSString stringWithFormat:@"%i : %i : %i", hours, minutes, seconds]];



What would a person have to add/modify to have it handle days? (Mainly that there calculation)

-- Lester

----
    
Change this:

[theField setStringValue:[NSString stringWithFormat:@"%i : %i : %i", hours, minutes, seconds]];

to this:

[theField setStringValue:[NSString stringWithFormat:@"%i : %i : %i : %i", days, hours, minutes, seconds]];


You will have to change that, but I dont know about the calculation.


----
Insert this after the original hour calculation:
    
days = hours / 24;
hours %= 24;


--EnglaBenny

----
I'm a newbie to cocoa but i haven't been able to figure out for the life of me how to subtract 2 dates and then display the results ie 10/17/2003 12:49:00 PM - Now then display the results as a formatted date like 00:00:00:00

--Chewtoy
----
%%C%%
NSDate *fromDate, *toDate;
.
.
.
NSTimeInterval seconds = [toDate timeIntervalSinceDate:fromDate];
%%/C%%
You can then easily calculate hours/minutes/seconds or whatever for input to NSString's +stringWithFormat: or -initWithFormat:

