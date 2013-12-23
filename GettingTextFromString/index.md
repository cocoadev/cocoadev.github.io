---
layout: page
title: GettingTextFromString
---

If I have a string containing about 20 lines of text, I want to get a substring that is between two ()'s on the ninth line how would I go about doing this?

----

If the string is definitely on 20 lines, then you can parse out the \n's using say componentsSeparatedByString:@"\n", grab the 8th object in this array and then use an NSScanner on it.

----
Also can use rangeOfString and substringWithRange to parse the ('s


----

m/\((.*?)\)/g; print $1;

*And how would one use this within a Cocoa application?*

--> exactly: how does one take advantage of PERL's fantastic text parsing?  is there a simple class for this?

A number of people have written wrappers for various RegularExpressions libraries; AGRegex is highly Cocoaish.

