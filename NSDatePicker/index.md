---
layout: page
title: NSDatePicker
---



I am trying to develop a core data application with a master/detail interface.  I pretty much have followed he tutorials in cocoadevcentral.com and the ones from the Apple site.  Everything seems to work just fine but for one small detail.  I have this NSDatePicker that when I create a new record I set the current date on it.  The problem is that the master TableView column is not being pdated.  It only gets updated if I increase/decrease the day/month/year.  Now I want the user to set the date if he/she want, but if they don't want to enter a date because the one that I set initially is appropiate then they should not fiddle with the control.  All my other fields are being updated just fine.  Any ideas of what could be going on?

Thank You

you have to subclass NSManagedObject contenct and use the awakeFromInsert method 
like so 
    
- (void)awakeFromInsert
{
        [super awakeFromInsert];

	[self setValue:[NSDate date] forKey:@"theKeyDate"];
}

see http://developer.apple.com/cocoa/coredatatutorial/coredata_mov13.html

----
is there any way that I could set specific background colors for specific dates on a graphical NSDatePicker? i.e. is it possible to have a calendar with the dates december 5 2006 and dec 15 06 having a green background?
the idea behind this is simple: i want to allow users to retrieve data for specific dates. i highlight the dates that have data that can retrieved, and then after the user selects one of these dates, i take the date and display its corresponding data. is this possible?

----

I think you would want to subclass NSDatePickerCell.

