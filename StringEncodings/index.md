---
layout: page
title: StringEncodings
---

See also:

[Topic]

I have a textfield where users can enter text in different languages, but how do I know what string encoding the entered text string is using?

----

The question does not make sense. All Cocoa objects deal with strings as simply strings with no given encoding. Encodings only come into play when you want to create things which aren't General/NSStrings, such as C strings or external files.

----

Thanks! I am trying to write the text entered by user into a structure as C String. And I need to know if it was Unicode or ANSI..

----

Ummm, if you really want to do this, General/NSString does use Unicode. ANSI is also a group that creates standards -- perhaps you meant ASCII? If you want to get the UTF-8 representation of the string (which is the same as a C string for ASCII characters, which include basic punctuation and English letters and numbers; UTF-8 also supports other languages), copy the results of the     UTF8String method of General/NSString into your own     char array (a.k.a. C String). --General/JediKnil

----

In 10.4 following group of statements are valid.

char pszName[20] = "12345";
General/NSMutableString *stringTitle = General/[NSString stringWithCString:pszName encoding:General/NSUnicodeStringEncoding];

and
General/NSString *title = [anObject copy];
const char pszName[230];
[title getCString:pszName maxLength:226 encoding:m_UseEnc];

how to do this in 10.2 & 10.3? General/THanks a lot!

----

Use the     initWithData: method. You'll have to get the length of the string using     strlen(), and create an General/NSData from that.

To go the other way, use the     dataUsingEncoding: method, which you can transform into a C string by appending a single null byte to the end.

see also General/NSStringToNSData
