---
layout: page
title: CalendarControl
---

AFCalendarControl is an iCal like bindings compatible control hosted at http://code.google.com/p/calendarcontrol. The project actually includes a second control; AFWeekControl. See that page for more details.

AFCalendarControl is dependent on my AppKit additions in another one of my projects Amber.framework which is available at http://code.google.com/p/amber-framework.

It exposes several bindings to access your model objects.


* AFCurrentMonthBinding, the month displayed by the control
* NSContentBinding, typically bound to the -arrangedObjects of an NSArrayController 
* AFContentDatesBinding, the collection subpath of the model objects' date property
* AFDateHighlightedBinding, another model subpath yielding a BOOL which determines if there should be a dot drawn beneath the day string
* NSSelectedIndexBinding, this is given instead of any selected date binding to allow for easier integration with the controller layer


Note: the control only expects a single object per date, once one is found it won't continue looking. If you have multiple objects sharing the same date in the NSContentBinding collection this will yield inconsistent results. Also, it is best to restrict the objects in the NSContentBinding collection to those in the AFCurrentMonthBinding range as expensive date comparison occurs.


A blank CalendarControl:

http://33software.com/images/components/CalendarControl-Blank.jpg

A populated CalendarControl illustrating the use of the AFDateHighlightedBinding:

http://33software.com/images/components/CalendarControl-Full.jpg


The control shadow isn't part of the view; it is drawn by setting the CoreAnimation shadow property.

