---
layout: page
title: KeyEquivalents
---

Should not this page really be titled E**'scapeSequences ??

"\r" for carriage return (ASCII 13)

"\n" for newline (ASCII 10)

"\t" for tab

"\\" for backslash

"\f" for form feed

"\v" for vertical tab

"\a" for alert bell

"\b" for backspace

"\"" for a quote mark, see below

----

They're part of ANSI C, and some of them (like \a) probably aren't applicable to Cocoa.

*Or did you mean the NS*Key constants here:* [http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSEvent.html]

----

How do I make an NSString just be one speech mark? E.g. @""" ?

The above doesn't work obviously, so what's the substitution for a speech mark in an NSString, have searched a lot of developer documents but no help.

----

Same as in C : @"\""

