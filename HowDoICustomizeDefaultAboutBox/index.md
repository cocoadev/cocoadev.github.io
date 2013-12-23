---
layout: page
title: HowDoICustomizeDefaultAboutBox
---

Hi again! I have Xcode 2.4, and I noticed that it places a default About box in my program. The last line of this About box reads "� __MyCompanyName__, 2007". How do I replace __MyCompanyName__ with my company's name (Ampersand Laboratories)? - Pietro Gagliardi, http://web.mac.com/pietro10

----

Edit the InfoPlist.strings file in your Xcode project. This also allows you to localize the copyright string.

----

Thanks! The About box displays the right text. I was wondering, will editing NSHumanReadableCopyright be enough, or do I need to edit more to further mark my app as copyrighted or by my company? Also, is NSHumanReadableCopyright a localized string or is it set to a fixed language?

----

Depending on the country where you live, copyright may require specific attributions, or may be bestowed as soon as a work is created. Mac OS X doesn't care--the only place it displays a copyright notice is in NSHumanReadableCopyright. As for localization, just localize InfoPlist.strings like you would for any other resource in your project.

----

Thanks. One more thing: How do I add the scrolling extra information box on some About boxes?

----
Add a     Credits.rtf or     Credits.html file to your Resources with the appropriate content, and it will find its way to your About box as if by magic.

----
Thanks - this is very helpful.

