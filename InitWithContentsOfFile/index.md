---
layout: page
title: InitWithContentsOfFile
---

Hi,

I am using NSDictionary initWithContentsOfFile: to read the contents of my file. It takes so much time! Is there any way to speed this up?

Thank you

----
nope, its just painfully slow.

----

Er. If you think about what -initWithContentsOfFile: does, it's easy to see why it takes the amount of time that it does. This method reads in an entire property list, parses it, and turns it into an NSDictionary object. That's a big job.

----

As an optimization note, make sure you only do it once and it really shouldn't be a problem. Unless your file is really huge, in which case you might want to think about how to better handle that (break the data up into smaller files and load them only when needed, for example)

----

I just used this method for the first time recently to read the XML music library file from iTunes.  This is a 1.2 MB file on my computer and it loads in about 2 seconds (and no, I don't have a GeeFive; it's an old Tibook 400).  What size file are you dealing with here and what kind of performance are you getting?  -- Bo

In relation to that. My program reads in that database file (iTunes Music Library.xml) and my 6mb file loads generally in < 10. This includes removing glyph's, parsing it into a dictionary, splicing out the songs and playlists and then cleaning up....so reading doesn't take that long. I do want to eventually rewrite it in CF calls, but for now it works, and my customers don't complain.

