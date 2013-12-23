---
layout: page
title: TextFormattingDifficulties
---



Okay, I've been doing a little writing about a project I'm working on in my page. A couple of difficulties have presented themselves:


* Bold and italic indicators don't appear to work while in lists (***this*** is a test of that), and
* Plurals are presenting themselves as a major problem. For instance, it took some creative phrasing to make the link General/StandardService fit where the plural was more natural. This, of course, takes us back to General/SingleWordLinks.


So, just curious as to what progress has been made with the links issue. I was also thinking that retaining the current system as well would probably be best, so that you can create pages with your normal Wiki links, and then just link to them later (so, no creation of pages via General/SingleWordLinks).

I'm not sure what you'd use for syntax, though, as there isn't really anything to compare it with at the current time. Perhaps something along the lines of:

\\%\\%BEGINLINK\\%\\% link \\%\\%LINK\\%\\% General/SomeWikiPage \\%\\%ENDLINK\\%\\%

Of course, I don't know how feasible any of this is. Just some thoughts for you. Keep up the good work, Steven!

-- General/RobRix

----

Bold and italic doesn't appear to work anywhere. I'm going to double check the syntax for that now...

-- General/RobRix

----

Well now, this is odd. It works appropriately in Internet Explorer 5.1 on my Dad's iMac, but not in General/OmniWeb rc1 on mine. Hmm...

-- General/RobRix

----

Probably an General/OmniWeb bug that got introduced in rc1--sometimes bold or italics wouldn't work, due to some, um, unintuitive behavior from General/NSFontManager. We've fixed the bug for the next release.

-- General/KurtRevis

----

Okay, thanks, Kurt. I look forward to rc2!

-- General/RobRix

----

Bold and italic works fine here in General/OmniWeb 4.0 final.

-- General/FinlayDobbie
