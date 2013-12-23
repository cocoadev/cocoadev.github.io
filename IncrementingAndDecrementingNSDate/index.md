---
layout: page
title: IncrementingAndDecrementingNSDate
---



I want to use a NSStepper to increment or decrement a date, by day, that is in a NSTextField with a Date Formatter on it.  Is this possible?  Here's the basic form i thought would work:

    
NSDate *date = [NSDate dateWithNaturalLanguageString:[dateField stringValue]];

if( 1 == [dateStepper intValue] )
	// increment date
elseif( -1 == [dateStepper intValue] )
	// decrement date

[dateStepper setIntValue:0];
	
[dateField setObjectValue:date];

----

Convert your date to a NSCalendarDate using     dateWithCalendarFormat:timeZone: and then use the     dateByAddingYears:months:days:hours:minutes:seconds: method.

You may be tempted to use NSDate's     addTimeInterval: method. For your purposes avoid it. NSCalendarDate will deal with Summer Time Saving adjustments for free. -- GCM

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSDate.html
http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSCalendarDate.html

----

Yeah, i used     addTimeInterval: before i posted here, and found it didnt work.  but NSCalendarDate worked great.   thanks.

