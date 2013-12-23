---
layout: page
title: ConversionToEncodingFailed
---

I am getting the following in the Run Log:

    TestApp[760] Conversion to encoding 30 failed for string "Bandyta wymuszaj?cy ..."


The code in question is:

    unsigned char * tempPtr = (unsigned char *) [string cString];


And the string contains what you see above, some weird characters.

What does this mean? And what should be done to make the conversion successful?

----
Never use -cString. It uses the "system encoding", which is not defined anywhere, and it is not guaranteed to be anything in particular. In almost every case, you should use -UTF8String. UTF-8 is ASCII-compatible and can represent every writing system known to man. Change your code and all will be well.

----

Thanks, but then how do I determine the length of the UTF8String?


----

    strlen( [string UTF8String] )


That will return the byte length of the string. [string length] will return the actual length (full characters) of the string.

----

Okay I was talking about the latter, and it looks like its not working for some reason... however I got it to work with:

    
unsigned char * tempPtr = (unsigned char *) string dataUsingEncoding:[[NSNonLossyASCIIStringEncoding] bytes];


----

UTF-8 is not ASCII-compatible; a UTF-8 code-point can be greater than 127, whereas this is illegal in ASCII. for ASCII-compatibility, you want UTF-7 (which is not supported by FoundationKit or CoreFoundation, but is supported by TextEncodingConversionManager) or non-lossy ASCII.

for most purposes, however, UTF-8 is just fine.

*--boredzo*

----

It *is* ASCII-compatible for suitable values of "compatible". What I meant by "compatible" was that any ASCII string is identical to its UTF-8 representation, and that any utility designed to work with 8-bit characters will generally work with UTF-8 strings, with caveats due to the fact that there is not a one-to-one mapping from character (or glyph, or whatever) to byte. You are of course correct that it is not "compatible" in the sense that it maps to ASCII, but that is an entirely different type of "compatible".

*On Tiger or later, you can use the new NSString method     cStringUsingEncoding and pass it the NSASCIIS<nowiki/>tringEncoding constant, which ensures full 7-bit ASCII compatibility. On Panther or earlier, however, your best bet is probably to use     UTF8String and prune out the characters past 127 - that is, if you **really** need to. --JediKnil*

This is not really right in either sense. First,     cStringUsingEncoding: will throw an exception if the string can't be represented in the given encoding, so if you pass NSASCIIS<nowiki/>tringEncoding and you have any non-ASCII data, it will throw, not just prune out bad characters.

The correct answer if you want pure 7-bit data, both on Tiger and pre-Tiger, is to either use the     dataUsingEncoding:allowLossyConversion: method along with NSASCIIS<nowiki/>tringEncoding, or to convert your string to a 7-bit encoding such as UTF-7, which as far as I can tell can't be done with any built-in NSString or CFString methods.

---- Other caveats include sorting and the fact that it is not safe to truncate / insert bytes at an arbitrary offset.

