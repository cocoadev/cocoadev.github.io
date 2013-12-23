---
layout: page
title: TurningOffAntialiasingInAnNSTextView
---

I'm trying to build a basic development editor.  Simply, I would like to turn off the antialising in the code editing window ala Xcode and General/SubEthaEdit.  I've sniffed around the Quartz and General/NSTextView docs online, but don't seem to be finding anything.

----

I don't see where General/XCode and General/SubEthaEdit override the default anti-aliasing behavior; they just tend to use fonts small enough that some user's prefs (set in the Appearance prefpane) tell them not to antialias text.  For example, I turned my minimum anti-aliasing size down to 8 and I use profont 9 and it's anti-aliased in both General/XCode and General/SubEthaEdit.  -- Bo

----

Check out the usesScreenFonts: and setUsesScreenFonts: instance methods of the General/NSLayoutManager class; I think these might be what you're after. --Wevah

----

I would like to turn off antialiasing in a General/TextView ala Xcode for a simple IDE I'm working on.  Can't seem to find any info in Apple's dev docs, any insights?

----

Monaco 10 is always non antialiased.

----

Does **Antialiasing** refer to the use of transparency to smooth edges in any color, or only to grayscale (for black and white images/text)?

----
Antialiasing refers to a fairly large number of situations which can be grossly summarized as "removing jaggies".
