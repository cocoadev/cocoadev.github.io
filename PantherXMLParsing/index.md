---
layout: page
title: PantherXMLParsing
---

Does anyone know whether Panther provides an Objective-C wrapper for the Core Foundation XML parser? I've put together a wrapper for the low-level parser, but given the impending release of Panther I'm not sure how much more effort to put into it.

----

yeah... there is a new class "NSXMLParser" 

----

Any API details? I'm presuming it takes a document, but does it give back a tree, or can you specify callbacks as decribed in the 'low level' parser documentation? (http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFXML/Tasks/ParsingXMLDocuments.html#//apple_ref/doc/uid/20001232/CJBJICFJ)

----

Panther also provides APIs for escaping and unescaping XML (and HTML) entities: CFXMLCreateStringByEscapingEntities and CFXMLCreateStringByUnescapingEntities.

----

NSXMLParser appears to wrap the CFXMLParser into a nice object. According to the release notes, it requires the presence of a delegate (which will obviously fulfil the duties of the callbacks in the CF-level object). http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSXMLParser.html#//apple_ref/occ/cl/NSXMLParser

----

It's a SAX-based parser rather than a DOM one, which means that it gives you notice of stuff it finds as it runs into it. I've successfully paired it with a recursive tree class and an XML model class to get some DOM-like results; the classes, CXLRecursiveTree and CXLXMLModel are under the BSD license, and when I get some real webspace up they're going to be a part of a framework, CXLCore, along with some of my HigherOrderMessaging stuff.

Anyhow, contact me if you want them and/or help using them.

-- RobRix

----

Now documented at http://developer.apple.com/documentation/Cocoa/Conceptual/XMLParsing/index.html.

