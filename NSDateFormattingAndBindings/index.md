---
layout: page
title: NSDateFormattingAndBindings
---

I have an NSTableView that is bound to an NSArrayController (that in turn gets the data from an NSMutableArray), how can I format an NSDate object that is within that array? Right now Its giving me "2004-11-24 14:00:00 -0500" if i initialize the NSDate with a language string.. and "Wednesday, November 24, 2004 2:00:00" if i use [NSDate date] to initialize it. I would like it to use a relative date, like Mail.app... ie. "Yesterday" or otherwise "2004-11-24" or something simple like that (ie. a short string instead of the huge one it is giving me by default).

I am aware that I can subclass NSDate or add a value transformer. But I am wondering what the best/recommended approach is? http://goo.gl/OeSCu

----

I'd use an NSValueTransformer/NSDateFormatter and do the following.

Theres an example here which I've added below. http://www.cocoabuilder.com/archive/message/cocoa/2004/6/23/110337

    
-(NSString *)stringForObjectValue:(id)obj
{
� �if(![obj isKindOfClass: [NSDate class]])
� � � �return nil;
� �
� �NSCalendarDate *inDate=obj;
� �if(![obj isKindOfClass: [NSCalendarDate class]])//if not calendarDate, 
then convert
� � � �inDate=[obj dateWithCalendarFormat:nil timeZone:nil];
� �
� �NSUserDefaults *theDefault=[NSUserDefaults standardUserDefaults];
� �int today=[[NSCalendarDate calendarDate]dayOfCommonEra];
� �int dateDay=[inDate dayOfCommonEra];
� �
� � � � � �
� �if(dateDay==today)
� � � �return [theDefault stringArrayForKey:@"[[NSThisDayDesignations"] 
objectAtIndex:0];
� � � �
� �if(dateDay==(today+1))
� � � �return [theDefault stringArrayForKey:@"[[NSNextDayDesignations"] 
objectAtIndex:0] capitalizedString];
� � � �
� �if(dateDay==(today-1))
� � � �return [theDefault stringArrayForKey:@"[[NSPriorDayDesignations"] 
objectAtIndex:0] capitalizedString];
� �
� �return [super stringForObjectValue:obj]; � �
}


If you don't mind not having the Today, Tomorrow,  Yesterday designations you can just use dateWithNaturalLanguageString, and %Y-%m-%d as your formatter string.

----

I'm trying to learn how to build my app with CoreData and Bindings (decided I'd had enough of the glue code) - and I think I've got myself in a pickle.

Quite simply I have a class in my data model with an NSDate/NSCalendarDate (model just says 'date') attribute in it.

I'd like to show this attribute in a TableView via bindings. But ah - NSTableViews don't know how to write a 'Date' in a text cell, so I looked up and wrote a Date->String NSValueTransformer subclass, and now my dates display fine.

However, I think I am correct in understanding that NSValueTransformer subclasses are only allowed to convert to the same class of object both ways... so because I am putting in a Date a String is popping out at the moment, if I implement the reverse method then it would still expect a String to be returned? Which will not fit with my data model obviously! 

It seems that I can do what I wanted (perhaps the Apple docs on NSValueTransformer are a little misleading here?).

There is still a problem with bringing dates out into a string, and then popping them back into the data model when they are changed in a tableview.

The documentation says to be aware of losing precision thorough an NSValueTransformer, yet that's exactly the position I find myself facing:

My model stores a Date.
This date stores an important time down to the second (i.e. I'm interested in the year, month, day, hour, min, and sec information).
I want my tableview in this instance just to display the year/month/day information.
I want the user to be able to edit this info without changing the hour/min/sec information in that date.
However doing things via the Transformer, the user comes along, sets a new date to '10/10/2005' and my transformer changes that attribute to be an NSDate with that year month and day, but with 12:00:00 for hour min second - i.e. not keeping my original hour min sec!

I think I will need access to the original date from the NSValueTransformer before changing it... but not sure how to do this :-S

----

*Use NSDateFormatter*

----

NSDateFormatter seems to do away with my need to use a ValueTransformer, which is great.

However, I still have the precision problem, if my MODEL must store a date complete with important hour/min/sec information, but I only need a particular VIEW to display and allow changes to the year/month/day information, when the user changes the date in that view, the time (hour/min/sec) is reset to 00:00:00 - this behaviour is not desired.

----

This is not a problem with Cocoa but of your view not meeting its requirements/obligations to the model.  The view editor must at least allow time down to seconds to be entered, if not also displayed.  A quick look at the NSDateFormatter shows a Long and Full style format string that includes hh:mm:ss but this is only available in 10.4.

----

I will not allow the user to change the date in the tableview, but will instead provide a button-cell that pops up a custom UI complete with all time information so that will suit the model requirements properly.

