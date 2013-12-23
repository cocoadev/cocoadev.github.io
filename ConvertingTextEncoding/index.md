---
layout: page
title: ConvertingTextEncoding
---

I am trying to convert existing strings' encoding type to others.
For example, I would like to change UTF-8 encoded NSString string to EUC-KR string.
Additionally, I would like to figure out what encoding method a given string has.
Can I do it with Cocoa and Core Foundation API? Do functions for that already exist there?
I couldn't find one.

----

NSStrings don't have encodings. You only get encodings involved when you "export" them to an NSData or a raw pointer. If you have data in one encoding that you want to change to another, you can do this very easily. Create an NSString from your data, giving it the right encoding, and then create an NSData from your string, giving it the encoding you want. If this process fails, that means your data can't be represented in that encoding.

----

So, is there no method to figure out what encoding a given string has?

----

Your question isn't really specific enough, but in either case the answer is, more or less, no.

If "a given string" is an NSString, then the question doesn't actually make sense, because there is no encoding.

If you're talking about a     char *, then there's no way to find out what encoding it's using for sure, because there are going to be several encodings it could map to. (Any bag of bytes is valid MacRoman and Latin1, for example.) It's possible to take a frequency count of the characters and make a best guess of the encoding based on that, but this is difficult and failure-prone. If you have a     char *, you *must* also have an encoding to go with it, either implicitly ("all strings in the Foo protocol are ISO-Latin1") or explicitly (prefacing all strings with a byte that indicates the encoding, etc.) in order to do anything useful with it.

