---
layout: page
title: CalendarWrapper
---

Programs like iCal and Mozilla Calendar use the iCalendar format to store events.  iCalendar is an open standard, see http://www.ietf.org/rfc/rfc2445.txt.  This page is list of all resources on OS X to work with the format.

vCalendar is an older version of the iCalendar format.

 

 *
 **
libical
**
http://www.softwarestudio.org/libical/

libical is an open source C library that parses the iCalendar format.  Mozilla Calendar uses libical and I believe so does iCal.  libical includes libicalvcal, a library for converting between iCalendar and vCalendar formats.

 *
 **
skyrix-xml
**
http://www.opengroupware.org/en/devs/sope/skyrix_xml/

This is an LGPL library that provieds a Objective-C interface to parse XML.  They also use libical to read iCalendar files.

 *
 **
iCalendarParser
**
http://www.oops.se/~malte/software.html

An iCalendar parser written in Objective-C. It's at a very experimental stage right now and only handles the inital parsing of iCalendar content lines. It works in a fashion similar to a SAXParser.

 *
 **
ics2vcs
**
http://www.isi.edu/touch/tools/#software

Perl script converts iCalendar (.ics) to vCalendar (.vcs).

 *
 **
CALCore
**

Apple's private framework for working with iCalendars.

