---
layout: page
title: HtmlToTxtDoesAnyOneKnowHow
---

I have a question about converting HTML escape code (you know & and % prefixes) into normal text.
Cam the web-kit be used as a parser??

Thanks..

----

how about     - (NSString *)stringByReplacingPercentEscapesUsingEncoding:(NSStringEncoding)encoding

*Replaces all percent escapes with the matching characters as determined by the given encoding encoding.*

[http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSString.html#//apple_ref/doc/uid/20000154/BCIECHFE]

That should get you at least half the way there.

Also see [http://cocoa.karelia.com/Foundation_Categories/NSString/_Flatten__a_string_.m]

----

Thanks.. I will try...

