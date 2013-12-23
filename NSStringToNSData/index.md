---
layout: page
title: NSStringToNSData
---




Could anyone give me a hand with converting an NSString to an NSData?

In reality, what I need to do is (as part of NSFile, my FILE wrapper for Cocoa) take an NSString and write it to a file. However, the NSString might be of any encoding, so I don't know whether using "cString" and "length" will work alright. Also, I need to be able to write little endian, if the case may be. My questions are these: a) how do you get the current encoding of an NSString? b) What would be the best way to write an NSString to a FILE, if the encodings are variable and so is endian?

Please, tell me if there already is an existing wrapper for Files, because I searched high and low and couldn't find one. It hasn't been that hard writing one, until now.

----

NSString does not have encodings. You give it an encoding to convert to when creating an NSData from it. If you don't know what encoding you should use, you need to think about it, you can't just pick at random. Generally, the best encoding to use when you aren't sure is UTF-8, but this is not suitable if the program that reads the data on the other end is expecting a different encoding. Endian issues don't normally come into play for strings. (They can for UTF-16, but Cocoa will probably include a BOM.)

Why are you writing a FILE wrapper for Cocoa? Aren't you just duplicating what NSFileHandle does?

----

Ah ha! So the string isn't actually stored using those encodings, but you can put it into one. I gotcha. My biggest problem is going to be the UTF-16, because I'm working with a particular game format which stores certain strings as a straight say, 100 characters, which is 200 bytes, but sometimes they are one endian, sometimes they are another endian. I don't want a BOM, that'll screw stuff up.

The reason I'm writing a FILE wrapper is because I love the way in REALBasic you can open a binarystream and use methods like "readShort" "readLong" "readDouble"... or their "write" counterparts. I would've subclassed NSFileHandle but there's a problem with the NSFileHandle implementation which makes it so you can't subclass it. There was a fix somewhere, but I couldn't get it to work. perhaps I could've used a category, but I like my class.

----

Cocoa and CoreFoundation do not let you control endian-ness of UTF-16 data, or the presence of a BOM. There's a Unix function, swab(), that can do the byte-swap for you, though. If you use UTF-8 data, endian-ness doesn't matter, it's built in to the format.

*This is not true. CFStringGetBytes() lets you control the presence of a BOM, and in 10.4, the kCFStringEncodingUTF16BE and related string encodings let you control endian-ness.*

----

One thing you could do is simply add a category to NSFileHandle with the methods you want, no need to subclass. If the methods are simple and don't need more instance variables, this seems like the way to go.

----

It's possible, and I actually did it for a little while, but it involved reading NSData of a certain size, casting, and plus I couldn't add the "littleEndian" ivar, which I know I could've worked around, but my method has served me alright.

see also HowToFindCharacterSetOfNSData

