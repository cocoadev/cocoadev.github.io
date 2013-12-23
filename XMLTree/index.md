---
layout: page
title: XMLTree
---

General/XMLTree is a Public Domain Objective-C wrapper for Apple's General/CFXMLParser which is provided in the General/CoreFoundation. Only basic access to the XML trees are available with v0.2.1 (December 2002).

http://iharder.net/macosx/xmltree/

General/XMLTree is useful if you need to work with XML with any version of OS X before 10.4. However, if working with only Tiger, General/NSXml works great.

----

Remember, to read the text from a CDATA node, be sure to use:
    
[[[tree  descendentNamed:@"name"] childAtIndex:0] description];

