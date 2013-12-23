---
layout: page
title: CFXMLParser
---

CFXMLParser is part of a set of Apple-provided C-language functions for manipulating XML documents. Documentation by Apple starts here: http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFXML/index.html.

XMLTree is a Public Domain Objective-C wrapper for these functions.

One simple way to begin making an accessible XML tree is like so:
    CFXMLTreeRef tree = CFXMLTreeCreateWithDataFromURL(
    kCFAllocatorDefault,
    (CFURLRef)[NSURL URLWithString:@"http://www.foo.com/foo.xml"],
    kCFXMLParserAllOptions,
    NULL );

Now you have a CFXMLTreeRef which points to the XML data. There are a number of functions available for accessing parts of the tree such as <code>CFTreeChildCount( tree )</code> and <code>CFTreeGetChildAtIndex( tree, 4 )</code>.

Don't trust Apple's examples on how to use the CFXMLParser, it's incorrect in many ways, always cross-check with the API docs. The low-level parser is a little tricky to get started with, but it works, although not as documented...

I recommend you use the Objective-C class XMLTree and contribute to its codebase if you need access to parts of the CFXMLTreeRef anatomy that it doesn't already support, but then I'm prejudiced: I use XMLTree and would like to see more functionality added.

There is also IconaraDOM, which is a full-featured DOM implementation for ObjectiveC.

See WritingXMLFiles on how to use CFXMLParser to create an XML document.

----

Be warned that DOM and other tree-based API (including, as far as I could tell, XMLTree) may use up a lot of memory and, ultimately, may not be very efficient. This is unlikely to ever be a problem when working with small documents on a desktop but it is very likely to cause problems when working with large files and/or working with many files. You may find that the low-level parser is more efficient and not that difficult to use after all.

See http://www-106.ibm.com/developerworks/xml/library/x-saxapi/ for a comparison between an event-based API and a tree-based API. The article discusses DOM vs SAX in the context of Java but the differences are similar to the high-level vs the low-level API in CFXMLParser. I have often found that XML newcomers like to start with a tree-based API (high-level API for CFXMLParser) because it is easier to understand but once they have tasted an event based API (low-level API for CFXMLParser), they never look back.

-- BenoitMarchal

----

It should be said that CFXMLParser is pretty low level, even in it's high-level version. If you develop for 10.3 or later NSXML is a bit easier (mostly because it uses all the things you are used to, NSStrings and delegates and whatnot).

On the subject of DOM vs SAX, SAX (stream based) is appropriate when you want to extract a part of the document, or just get the semantics of it (what it *means*), DOM (tree based) is appropriate if you are working with the actual contents and perhaps want to reorder parts of the document, or want to extract parts depending on the value other parts (which can be tricky with SAX).

--TheoHultberg/Iconara

----

Beware that CFXMLParser is a pain in the butt when it comes to entities (such as &amp;). It treats them as separate nodes, so you have to handle them each manually. Read more here: http://www.cocoabuilder.com/archive/message/cocoa/2002/2/27/14147

Unfortunately, there doesn't seem to be a good **BSD**-based DOM XML reader and writer that works on 10.3 and below. If you are targeting 10.4, NSXML is the best. Any ideas? I'd rather not write a new Cocoa wrapper for libxml or expat if I don't have to.

