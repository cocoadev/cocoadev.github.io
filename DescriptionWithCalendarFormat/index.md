---
layout: page
title: DescriptionWithCalendarFormat
---

The following is a list of standard formatters for use with NSCalendarDate's     -descriptionWithCalendarFormat: method.


* %%    - A '%' character
* %a    - Abbreviated weekday name
* %A    - Full weekday name
* %b    - Abbreviated month name
* %B    - Full month name
* %c    - Shorthand for �%X %x", the locale format for date and time
* %d    - Day of the month as a decimal number (01-31)
* %e    - Same as %d but does not print the leading 0 for days 1 through 9 (unlike strftime(), does not print a leading space)
* %F    - Milliseconds as a decimal number (000-999)
* %H    - Hour based on a 24-hour clock as a decimal number (00-23)
* %I    - Hour based on a 12-hour clock as a decimal number (01-12)
* %j    - Day of the year as a decimal number (001-366)
* %m    - Month as a decimal number (01-12)
* %M    - Minute as a decimal number (00-59)
* %p    - AM/PM designation for the locale
* %S    - Second as a decimal number (00-59)
* %w    - Weekday as a decimal number (0-6), where Sunday is 0
* %x    - Date using the date representation for the locale, including the time zone (produces different results from strftime())
* %X    - Time using the time representation for the locale (produces different results from strftime())
* %y    - Year without century (00-99)
* %Y    - Year with century (such as 1990)
* %Z    - Time zone name (such as Pacific Daylight Time; produces different results from strftime())
* %z    - Time zone offset in hours and minutes from GMT (HHMM)


----

**Example**

(From NSCalendarDate documentation)

*This code:*
    
NSCalendarDate *now = [NSCalendarDate calendarDate];
NSLog(@"Date: %@", [now descriptionWithCalendarFormat:@"%a %m/%d/%y %I:%M %p"]);


*Produces this result:*

    
Date: Tues 02/21/06 9:46 AM

 Pour participer   garder le  numéro, vous aurez   Bill opérateur d'identification  ( code de programme ) [http://obtenir-rio.info numéro rio]. Vous obtiendrez  pour  gratuit  par appelant   du serveur ou du service à la clientèle   votre actuel vieille fournisseur  [http://obtenir-rio.info/rio-bouygues code rio bouygues] . Vous ne  mai   obtenir  un SMS avec votre . Avec votre  [http://obtenir-rio.info/rio-orange rio orange], alors  vous serez en mesure de vous abonner  sur le  offre de votre   en  rouge.

