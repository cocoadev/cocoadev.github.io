---
layout: page
title: ReadHTMLDataIntoNSString
---

I've downloaded an HTML file into an NSData, but I don't know how to put the data in an NSString. I'm using Panther, by the way.

----
    [[NSString alloc] initWithBytes:[data bytes] length:[data length] encoding:*someEncoding*]

If you don't know the encoding, consider using     -[NSString initWithContentsOfURL:usedEncoding:error:] instead of pulling your HTML into an NSData.
----
initWithContentsOfURL only works for Tiger. Thanks. On the site, it says that the charset is windows-1252. That's N<nowiki/>SWindowsCP1252StringEncoding, right?

----

Yep, otherwise known as Windows Latin-1.  You could also check the WebKit framework for encoding-aware HTML download. I bet there's something there.

----

Actually, Apple says that WebKit is "not suitable for implementing non-GUI applications".

