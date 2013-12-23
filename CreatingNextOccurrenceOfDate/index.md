---
layout: page
title: CreatingNextOccurrenceOfDate
---



I have an NSTextField where a user can enter a date in any format desired, and it is automatically formatted into an NSDate.  What this also means is that the user does not have to put in a year.  What I want to happen when the year is omitted is that the date that is created is the next (year's) occurrence of the date entered.

For example... if on February 1st, 2005, the user enters "1/1" in the field, the date that should be created is January 1st, 2006.  However, if  "1/1/05" is entered, the date created would be January 1st, 2005, which has already passed.  However, when you do something like     NSDate *date = [NSDate dateWithNaturalLanguageString:[dateTextField string]];, the current year is entered for the year, if the user doesn't specify one.  The problem is that I can't check to see if a user entered a date without a year BEFORE I convert to the date, because I don't know exactly how they will enter it (they can use any format that     dateWithNaturalLanguageString: can decipher).  Does anyone see some sort of solution to this?  Thanks.

--LoganRockmore

----

take the length of the string the user entered - if it contains more than one '/', leave it alone; if less, convert the date to
your own specs. You will also have to check for other types of valid date strings such as those containing '-'.

----

Apple has reccomended against using dateWithNaturalLanguageString:. Use a date picker under Tiger, or text field requiring precise format (using the date format from the user's locale settings).

