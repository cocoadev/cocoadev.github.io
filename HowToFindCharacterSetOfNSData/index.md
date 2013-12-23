---
layout: page
title: HowToFindCharacterSetOfNSData
---



I am working on a small project which has multiple user interfaces (web/cocoa). This is a project across different global zones - America, AsiaPac, Euro. One of the screens has interface for user to upload files. The WO engine for web-objects provides NSData of the file being uploaded which is saved into the database.

When one of my Cocoa programs retrieves this NSData object, I want to be able to identify what is the character set of this NSData object. The character sets are all standard ones as in unicode.org. Is there any API to get this ? Is it possible to identify  "this NSData object has UTF8 character set" ?

Thanks,
Amit

----

Short answer: No.

Long answer: A character set is a way of interpreting a blob of binary data as text. Any given blob can be interpreted in a number of different ways. It's possible that a given blob won't be legal in some character sets, but that doesn't necessarily give you any useful information. Programs where making an educated guess is useful (web browsers) use statistical methods on the data to make a guess as to which character set is being used on a given set of data, but this is not 100% reliable. If you absolutely must do this, that is the only way to do it, but you should really really try to make the user tell you the encoding of his files, or come up with a way to guess extremely reliably based on the user's language or region.

----

The Mozilla project has a page on their character set guesser, chardet.  http://www.mozilla.org/projects/intl/chardet.html

I haven't looked at it myself, but it seems like an interesting problem.  I don't know about Mozilla, but I've read about how impressive IE is at char set guessing.

see also NSStringToNSData

