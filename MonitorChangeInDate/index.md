---
layout: page
title: MonitorChangeInDate
---



Hello,
Does anyone have an idea how to be automatically informed when the date has changed i.e if the program runs today at 8.00pm then it should be informed when the time is 12.00AM i.e the start of the next day? I ask this because I have an icon indicating the current day(e.g 3 for 3rd, 21 for 21st) and would like to change it automatically when the day changes.

Polling is an idea, running an NSTimer or something but is there some sort of NSNotification for this?
Thanks
Sapsi

----
You do not need to poll. If it is 8PM then the date will change in 14400 seconds; set up a timer with that duration. The math is generally easy, and NSCalendar/NSCalendarDate make it fairly easy to figure out the duration from the current time to the next change in date.

----
That will not work for DST changes. --K

----
Of course it will work for DST changes. You don't take the current hour and then subtract it from 24. You ask NSCalendarDate to give you the time of midnight, tomorrow, then subtract the current time from that time. NSCalendarDate knows about DST changes, leap seconds, and any other things which might arrange for the day not to equal 86400 seconds, and will take them into account. That's why I suggested it rather than doing the math directly.

----
So then what if I change my time zone while the app is running?  This is not an infeasible scenario (taking a train or plane across timezone boundaries with my notebook).  As described below, there's absolutely nothing wrong with polling with appropriate granularity, such as 1 second or 1 minute. --K

----
This is why kCFTimeZoneSystemTimeZoneDidChangeNotification exists.

You'll still need to track changes to the system clock. This can be done by using the kEventSystemTimeDateChanged Carbon event.

You can poll if you want. But polling is inelegant and inefficient. This can be done without polling without a great deal of trouble, and should.

----
Please properly format your pages and avoid MailingListMode!

In answer to your question, the application itself can run a simple timer that fires every minute or so while it's running, changing the icon if needed. There is positively nothing wrong with using periodic timers in this way. don't give in to premature optimization. Also, when your app's not running, the icon is not updated. Apple's own iCal works this way. This is because, in order to change the icon of an inactive app, you'd have to have a daemon app running in the background that has administrative (or at least write) access to the application's bundle, to change the icon itself. Then there's the problem of having to forcibly restart the Dock to get docked apps to reflect this if inactive ... so, Apple doesn't do this for several very good reasons.

----
You are incorrect regarding iCal.  It uses a dock extra: http://jens.ayton.se/blag/the-mysteries-of-ical-revealed/ --K

----

Well, not totally incorrect: "One of the minor yet shiny new features of Mac OS X **10.5** is that iCal�s dock icon now shows the correct date even when iCal is not running." (emphasis mine)  

So, up until 10.4.11 it was static.

