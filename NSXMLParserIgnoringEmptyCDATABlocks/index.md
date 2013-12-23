---
layout: page
title: NSXMLParserIgnoringEmptyCDATABlocks
---

It seems that Tiger's NSXMLParser won't recognize empty CDATA blocks.
When parsing the following XML snippet it will only send parser:foundCDATA: for the body element, the subject element is ignored entirely!

    
<subject><![CDATA[]]></subject>
<body><![CDATA[<b>Hello</b>]]></body>



Is this a bug we should report or am I overlooking something obvious?

-- ElwinZuiderveld
----

A CDATA block isn't like a tag; an empty CDATA block is semantically nothing.  I wouldn't expect to necessarily receive notification.

----
You have a point, however, Panther's NSXMLParser _does_ parse empty CDATA blocks and now I need to find a solution to get my project running under Tiger.

I'm curious which of the two is the correct/preferred behaviour.

----
Why do you need to detect the CDATA anyway? It should be equally valid to write this, no?

    
<subject></subject>
<body>&lt;b&gt;Hello&lt;/b&gt;</body>


----
Yes, but that's not really the issue here. The above code is just an example.

I could of course do a quick search/replace and replace the empty CDATA block with something 'useful' before parsing.

Not really a nice solution is it? Does anyone have another suggestion?

----

I agree with the poster above, an empty CDATA-section should not be reported, since it's semantically equivalent to an empty string, and the parser doesn't report emtpy text nodes before and after every element node, does it? Remember that CDATA-sections are just a way of escaping information, not special constructs. Many parsers don't report CDATA-sections at all, but treat them as escaped text nodes. --TheoHultberg/Iconara

