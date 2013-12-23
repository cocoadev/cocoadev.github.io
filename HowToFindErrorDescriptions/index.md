---
layout: page
title: HowToFindErrorDescriptions
---



I'm working with NSXMLParser, and I'm getting an error during parsing.  The NSError has the following information:

*[myError localizedDescription]     is   "Error NSXMLParserErrorDomain 201"
*[myError localizedFailureReason]   is  (null).


It doesn't seem to me that any of the error codes described in the NSXMLParser documentation have a code of 201 (I haven't tested exhaustively, but if they're listed in order, then none of them do).

So, how do I find out what this error is?

----

Well, the header file doesn't list that error code either. Log a documentation bug from the appropriate ADC reference library page, and maybe one in RadarWeb too, and ask on the Cocoa-Dev mailing list. Someone there might know.

