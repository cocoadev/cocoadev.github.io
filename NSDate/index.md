---
layout: page
title: NSDate
---

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSDate_Class/index.html#//apple_ref/doc/uid/TP40003641


*ComparingNSDates

----

NSDate inherits from: NSObject

An NSDate object stores a date and time that can be compared to other dates and times.

One of the coolest features of NSDate is the Natural Language feature:

+ dateWithNaturalLanguageString:(NSString *)string

Creates and returns an NSDate set to the date and time specified by string. The argument string can be a colloquial specification of a date, such as "last Tuesday at dinner," "3pm December 31, 2001," "12/31/01," or "31/12/01." In parsing the string, this method uses the date and time preferences stored in the user's defaults database. (See dateWithNaturalLanguageString:locale: for a list of the specific items used.)

----

+ dateWithNaturalLanguageString:(NSString *)string

Is a really cool feature.  It doesn't appear as if the opposite function exists: naturalLanguageStringForDate or something similar.  Is there some way to do this easily or automatically?  For example, in my program I want to say "Last Update: Today at 11 AM" instead of "Last Update 11/21/04 at 11 AM"... or in the extreme case: "Last Update: Yesterday at 11 AM".  I'd also like to have the ability to say "Next Update: Today/Tomorrow at 11 AM".  

The software update preference pane has this functionality.

Any insight/help would be great.  Thanks, Joe

----

The answer ought to be NSDateFormatter, but it doesn't actually have this functionality.  Here's a method from the cocoadev mailing list < http://www.cocoabuilder.com/archive/message/cocoa/2004/6/23/110337 >.

