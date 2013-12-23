---
layout: page
title: CharacterQuestion
---

I have this:

\\u201a\\304\\364

Which I know is the ' character. Does anyone know what format that is and how I can actually turn it into something meaningful?

Its produced by a carbon app if that helps at all.

Thanks in advance,

-- MatPeterson

----

Come again? This is actually 3 characters. First one given as a unicode character, the next two given as octal numbers.

It looks like a standard C string with escape codes (for the characters), although there are double slashes.

In what context do you want to use this?

Well as far as I know that is one character (I figured it was a mess). I want to simply turn it into an NSString that I can use happily.

*He probably got the double slashes from printing the string to the screen in project builder gdb.  Er, I'm not much help though. :-)*

If you want an NSString with a certain character, simply do: @"<the character>";. If the character is an 8 bit ISO 8859-1 character, you can still type it, as long as your source file encoding is ISO 8859-1 (Latin 1) -- but if it is not, you can either use UTF8 encoding and type: [NSString stringWithUTF8String:"<the character>"];, or, you can ignore the source file encoding and type: unichar ch[] = { 0x201a }; [NSString stringWithCharacters:ch length:1];.

Hope it helps, --AllanOdgaard

I don't think it's a matter of typing the character, he knows it's a single quote.  The question is about doing an encoding conversion, I guess.

I think thats a console output. I think the PB console can only show ASCII(0 - 127) characters correctly, 127+ are escaped. 

The PB console assumes output to be in UTF8 -- e.g. "puts([@"�blegr�d" UTF8String]);" should display just fine, perhaps the original poster could state exactly what he is doing, where he get the string from, what he wants to do with it etc. --AllanOdgaard

---- 

It was an output in console. I am assuming that it would look like a glyph...assuming though. It was a character from a file I am reading that displays fine in a carbon app but I want to port it to cocoa. I want to create an NSString to use in the UI. I have not tried cstring at this time but I am fairly certain it will not work.

*
I really do not understand any of this -- do you have the unicode value for a character that you wish to display? that should be quite simple... do you only have the visual representation for the character (i.e. the glyph) and have tried to obtain the unicode value through console or similar (and the above is what you got), and now you want to display it yourself?
*

----

Restart from scratch then...

I am parsing in a file that contains some random characters (I assume they are glyph representations as they are produced by a Carbon application). That chracter shows up in the carbon app as ' but when I parse the file and place that single character into console, it shows as above.

When I place it in an NSString it turns into a lovely glpyh mess (3 characters) that I cannot do anything with.....I can send the file in question or the character in the file in question...but that character above is all it is showing from console.

*
The Console is really for short error messages and AFAIK cannot handle anything besides 7 bit ASCII, at least I just tried to print some accented characters as both UTF8, MacRoman and ISO Latin 1, and in neither of the encodings, did they show up correctly.

With regard to parsing your file -- you must know which encoding the file is in and 'decode' this to whatever you work with internally in your program. I.e. if the file is MacRoman, and you read it as ISO Latin 1, then 8 bit characters will be messed up. From a terminal you can use "hexdump -C" to get a hexdump of the file, which should help you look at the contents, further more, if you have iconv installed (comes with Fink) you can try various things like "iconv -f mac -t utf8 <file" and see if it outputs correctly (here i assume you have set the encoding of your Terminal to utf8).

Encodings are really a mess and I think the problem you have arise because you either read it in the wrong encoding, work with it in the wrong encoding, write it in the wrong encoding or sends it to an output device (like the Console) in the wrong encoding...
*

I totally agree. I have tried every encoding and none of them work....

