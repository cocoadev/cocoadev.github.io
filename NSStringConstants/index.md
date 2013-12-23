---
layout: page
title: NSStringConstants
---

Just started using XCode version 3.0. The first clean build I attempted on a project I got a couple of warnings about NSString constants.

**warning: input conversion stopped due to an input byte that does not belong to the input codeset UTF-8**

The culprit was a format string that has a degrees symbol **Angle (%i�)**. What is the proper way to create an NSString constant that has UTF16 characters? A quick way to kill this particular warning was to just sub the UTF16 character with a **%C** format entry.

    
NSString *angleString = [NSString stringWithFormat:@"Angle (%i%C)", angle, 0xb0];



----
That is the proper way. The     @"" syntax does not accept anything other than ASCII.

----

You keep talking about UTF-16, but 0xb0 is a perfectly valid ASCII character, and most certainly not a valid UTF-16 encoding.  And doesn't %C mean to read sizeof(int), which would be 4 bytes?

----
Wrong on all counts. ASCII is 0-127 (in hex, 0x00 through 0x7f) and so 0xb0 is not a valid ASCII character at all. It is indeed a valid UTF-16 character, which shares the code space with Latin1 through 255 (0xff), so that 0xb0 in Latin1 is equivalent to 0x00b0 in UTF-16 (and of course the leading zeroes are optional), producing the degree symbol. %C means to read a unichar, which is two bytes. But of course with C variadic functions, all parameters of an integer type smaller than     int get promoted to int, so it is perfectly valid to pass any such type. And lastly, that is irrelevant anyway, because 0xb0 *is* of type     int.

----

The warning says that an input byte does not belong to the "input codeset UTF-8". The UTF-16 character 0xb0 resolves to the two byte sequence {0xc2, 0xb0} in UTF-8 encoding. One thing I didn't mention is that the degree symbol pasted into the source code is being printed/drawn correctly in my Cocoa app. Since I have XCode set to save files in the default UTF-8 encoding the symbol character is being written to disc as {0xc2, 0xb0}. This warning was not being generated in XCode versions before 3.0. 

----
Regardless of all that, it is not permitted to use non-ASCII characters in NSString constants, so the rest is somewhat academic.

