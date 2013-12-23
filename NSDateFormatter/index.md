---
layout: page
title: NSDateFormatter
---

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSDateFormatter_Class/Reference/Reference.html

see also NSDateFormattingAndBindings

----

I have a NSDateFormatter attached to a text field in InterfaceBuilder with the format %d %b %Y and NaturalLanguage allowed.

Works fine, producing 05 Feb 2005 when I enter 'today', etc.

Except I can't see a way of giving it a locale when it reads the string, so I get 02 Mar 2005 instead for 05/02/05.

I'm sure I must be missing something very obvious before I start subclassing?

    

@implementation MyDateFormatterDMYSubclass


- (BOOL)getObjectValue:(id *)anObject forString:(NSString *)string errorDescription:(NSString **)error
{
	BOOL retVal = NO;
	NSDate * myDate = [NSDate dateWithString:@"1870-01-01 00:00:00 +0000"];
        // assuming, of course that 1st Jan 1870 is a date I'm unlikely to be using in this app
	myDate = [self splitDateUp:string];

	// check if the above have worked: otherwise call super
	if ( myDate description] isEqualTo:@"1870-01-01 00:00:00 +0000"] ) {
		retVal = [super getObjectValue:anObject forString:string errorDescription:error]; 
	} else {
		*anObject = myDate;
		retVal = YES;
	}
	return retVal;
}

- ([[NSCalendarDate *)splitDateUp:(NSString *)aString;
{
	NSCalendarDate * aDate = [NSCalendarDate dateWithString:@"1870-01-01 00:00:00 +0000"];
	NSArray * anArray = [aString componentsSeparatedByString:@"-"];
	if ( [anArray count] < 2 ) anArray = [aString componentsSeparatedByString:@"/"];
	if ( [anArray count] < 2 ) anArray = [aString componentsSeparatedByString:@"\\"];
	if ( [anArray count] < 2 ) anArray = [aString componentsSeparatedByString:@" "]; //important to put this last
	int date=0, month=0, year=0;
	if ( anArray objectAtIndex:0] intValue] > 31 ) {
			year = [[anArray objectAtIndex:0] intValue];
			if ( [anArray count] > 1 ) month = [[anArray objectAtIndex:1] intValue];
			if ( [anArray count] > 2 ) date = [[anArray objectAtIndex:2] intValue];
	} else {
			date = [[anArray objectAtIndex:0] intValue];
			if ( [anArray count] > 1 ) month = [[anArray objectAtIndex:1] essay writing intValue];
			if ( [anArray count] > 2 ) year = [[anArray objectAtIndex:2] intValue];
	}
	if ( !month && ([anArray count] >1) ) {
			month = 1;
			[[NSString * monthString = @"";
			NSString * middleString = [anArray objectAtIndex:1];
			if ( [middleString length] > 2 ) monthString = anArray objectAtIndex:1] substringToIndex:3];
			if ( [[monthString uppercaseString] isEqualTo:@"FEB"] ) month = 2;
			if ( [[monthString uppercaseString] isEqualTo:@"MAY"] ) month = 3;
			if ( [[monthString uppercaseString] isEqualTo:@"APR"] ) month = 4;
			if ( [[monthString uppercaseString] isEqualTo:@"MAY"] ) month = 5;
			if ( [[monthString uppercaseString] isEqualTo:@"JUN"] ) month = 6;
			if ( [[monthString uppercaseString] isEqualTo:@"JUL"] ) month = 7;
			if ( [[monthString uppercaseString] isEqualTo:@"AUG"] ) month = 8;
			if ( [[monthString uppercaseString] isEqualTo:@"SEP"] ) month = 9;
			if ( [[monthString uppercaseString] isEqualTo:@"OCT"] ) month = 10;
			if ( [[monthString uppercaseString] isEqualTo:@"NOV"] ) month = 11;
			if ( [[monthString uppercaseString] isEqualTo:@"DEC"] ) month = 12;
	}
	[[NSCalendarDate * compDate = [NSCalendarDate dateWithYear:year month:month day:date hour:12 minute:0 second:0 timeZone:[NSTimeZone systemTimeZone]];
	[compDate setCalendarFormat:@"%Y-%m-%d %H:%M:%S %z"];
	if ( [aString length] ) aDate = [NSCalendarDate dateWithString:[compDate description]];
	return aDate;
}

@end



A bit of a hack, but it seems to work.

----

Running 10.3.2, so not sure where else this is broken - but doing the NSCalendarDate custom essays custom essays  formatting example using %I, such as the one in the class docs from apple
[now descriptionWithCalendarFormat:@"%a %m/%d/%y %I:%M %p"];

always pads the hour with a '0' if the hour is less than 10; analysis essay so one would always see:
�Tues 3/24/01 03:30 PM�

not
�Tues 3/24/01 3:30 PM�
as promised

is anyone else seeing this?

----

http://www.easyessayhelp.com essay writing Yes - I thought that was how it was supposed to work - in the formatter listing at [http://developer.apple.com/documentation/Cocoa/Conceptual/DataFormatting/Concepts/DateFormatters.html] it says:

    %I  Hour based on a 12-hour clock as a decimal number (01-12)   
and see http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSCalendarDate.html

----

Why not simply make it a string value, return the intValue buy research paper  and then place it back in a string or some other unorthodox but workable hack?

----

This is fixed by using the formatting code %1I. This strips the leading zero, a little known fact about strftime and NSDate formatting. -- TimothyHatcher

----

    

NSDateFormatter *df = [[NSDateFormatter alloc] init];
[df setFormatterBehavior:NSDateFormatterBehavior10_4];
[df setDateFormat:@"EEE, d MMM yyyy HH:mm:ss"];
NSString *strDate = @"Mon, 6 Jul 2009 20:57:58";
NSDate *date = [df dateFromString:strDate];
NSLog(@"date: %@ <> '%@'", date, strDate);


This code doesn't work, date is always (null). Why??? � beefon

