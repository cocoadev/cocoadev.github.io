---
layout: page
title: StringLanguage
---

Good day.

I have some questions:

What is the simplest way to detect language of General/NSString(or character of General/NSString)?
How can i detect active keyboard layout?
Where can i read about working with (active) keyboard layout(s)?

Thanks.

*What language a string is in? I'm not aware of any way to do that without writing an AI parser. You could search the string for common words from common languages, I guess. 'the' would mean English, 'le' would mean French, 'el' would mean Spanish, and so on.*

The     General/AppleLanguages key in the General/NSGlobalDomain of user defaults contains the current OS language.

http://developer.apple.com/documentation/Cocoa/Conceptual/General/UserDefaults/Concepts/General/DefaultsDomains.html

Take a look at General/HIToolbox/Keyboards.h
----

General/NSString(s) have encodings. See General/NSStringEncoding.

For input managers and what not, see:


http://developer.apple.com/documentation/Cocoa/General/TextInternational-date.html

- General/NeilVanNote

Hello, I might be able to help you in case you're looking for special cases in terms of language. There's General/NSCharacterSet, a cool class which lets you define a range of letters and symbols. That way I wrote a parser to divide German/Persian text files into seperate strings. Check the manual or mail me at info a.t sprachwerker.de
