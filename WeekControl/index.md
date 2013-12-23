---
layout: page
title: WeekControl
---

General/WeekControl is a subproject of General/CalendarControl hosted at http://code.google.com/p/calendarcontrol. It draws a more convenient way of representing the weekdays than having seven separate checkboxes. The control is fully resolution independent and is available under the New BSD license.

General/WeekControl is dependent on another one of my projects Amber.framework which is available at http://code.google.com/p/amber-framework.

It access' your model data using datasource callbacks as part of a formal protocol:

    
@protocol General/KDWeekViewDataSourceProtocol <General/NSObject>
- (BOOL)weekview:(General/KDWeekControl *)view isEnabledForDay:(Weekday)day;
- (void)weekview:(General/KDWeekControl *)view setEnabled:(BOOL)enabled forDay:(Weekday)day;
@end


http://33software.com/images/components/General/WeekControl.jpg
