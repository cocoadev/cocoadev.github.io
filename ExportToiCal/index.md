---
layout: page
title: ExportToiCal
---

Anyone have a good idea or way of exporting information to iCal? I've seen apps which have this functionality, where you just export and it opens iCal and asks you which calendar the new data should be put into. Is there a framework that iCal uses for this or something? Any help is much appreciated. --LoganCollins

*No framework, just simple Apple Events (usually sent in form of AppleScript<nowiki/>ing). -- EmanueleVulcano aka l0ne*

You could also generate a vCalendar ( http://www.imc.org/pdi/ ) file, then subscribe to this file in iCal

----
Simply add the events to the appropriate calendar's .ics file in iCal's application support directory. Also be sure to delete the index in the same directory in order to force iCal to regenerate it.
The .ics files here are standard iCalendar files that most any calendar program can generate. The format is described in RFC 2445, 2446 and 2447. (http://www.ietf.org)

----
Ok, understood. But how i do force ical to regenerate the file? AppleScript? And is it possible to read the iCalendar data through the index file (for rapid reading)? --MistaC (Chris Obdam)

I would also like to know how to do this. Thanks for any help. --LoganCollins

