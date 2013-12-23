---
layout: page
title: SeekingSafeWayToRequestAMonospaceFont
---

Is there a "reasonably safe" (i.e., "somewhat general", non-installation-specific)
way to ask for a monospace font when using the NSFont class?

I just want to set a monospaced font for some results I am displaying in a text view.

How much will I be hosed for asking for something like "Courier" by name?
On most people's systems?

----

Try     [NSFont userFixedPitchFontOfSize:0.0] to get the user's default monospace font and size.

----

Aha. Just a hole in my knowledge of font terminology. Didn't realize it was horizontal as well as vertical.
Thank you. I guess there is no guarantee of any particular font always being present on a system.
I see people complain all the time that their Terminal doesn't work and it turns out that it is because
they are missing the Monaco font.

*well, that's their problem then. Moncaco lives in /System. i.e. it's untouchable. there's no guarantee of anything at all being present on their system if they're going to do something like that.*

**I've also had to do support over the phone and it turns out the person (a designer) recently cleaned up on his system by deleting all the fonts he didn't need! ;)**

*i once had a designer ask me if it was ok to delete everything in /bin, /usr and so on, since he "never used anything in there and didn't believe in the command line"*

----

You must realize that starting with panther, it is possible to disable fonts via FontBook.  See: http://diveintoosx.org/panther/font_management.html

JoeCrow

