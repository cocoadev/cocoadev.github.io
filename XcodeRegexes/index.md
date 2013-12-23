---
layout: page
title: XcodeRegexes
---



Does anyone have any information about the format of the regular expressions used in Find in Xcode? I've tried to look, but I haven't found any documentation explaining the format.

Xcode 1.5 is great, but it still crashes now and then, and it needs more documentation on the advanced features.

- Petteri

----


*Step 1. Open Safari
*Step 2. Google "Xcode Regular Expressions"
*Step 3. Check out the search results


Here's a quick result that looks like it might help:
http://lists.apple.com/archives/xcode-users/2004/Oct/msg00303.html

----

Thanks for the link. I searched Google, but most pages talk about the Smart Groups regex format. Your page, however, seems to indicate that Find uses Perl compatible regexes. That's great. I wonder why Apple hasn't documented that anywhere. Well, thanks again for the pointer.

- Petteri

----

Also wanted to say thanks for the link. I never heard of ICU before. How does it compare to the other commonly mentioned regular expression libraries (e.g. PCRE, General/MOKit, General/OgreKit, Omni Frameworks)?

-General/EricWang

----

The link in the email is now obsolete (it seems to forward to a generic IBM open source page).

The direct link for ICU appears to be 
http://www.icu-project.org/

Substitution in replacement uses \n where n is the number of the part that should be subsituted \0 is the whole match, 1 is the first subpattern, etc

http://www.icu-project.org/userguide/regexp.html
http://www.icu-project.org/userguide/unicodeSet.html
