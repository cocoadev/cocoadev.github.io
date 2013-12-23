---
layout: page
title: WhatIsANonPrintingCharacter
---

From my OS 8 days I thought I'd remembered a function which would - given an ACSII code - tell me if the corresponding character is printable or not.
Did this function actually exist?
Is there something in Cocoa like it?

-- Alex

----

Well... I can answer this.

illegalSet = General/[NSCharacterSet illegalCharacterSet] will give you a character set of the illegal characters.

You can then use [illegalSet characterIsMember:uChar] to determine if uChar is illegal. Note this wont tell you if the character exists in a font, but it will always be printable.

Will

----

Thanks!

***Page orphaned***
