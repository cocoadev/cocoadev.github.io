---
layout: page
title: NSXml
---

NSXML is a set of classes introduced in OS X 10.4 (Tiger) that deals with a complete XML tree.

It is comprised of these FoundationKit classes:

*NSXMLNode
*NSXMLDocument
*NSXMLElement
*NSXMLDTD (wikified as NSXmlDtd)
*NSXMLDTDNode

It does not include NSXMLParser, though NSXML does use it to parse XML.

Apple's unusually good overview is at < http://developer.apple.com/documentation/Cocoa/Conceptual/NSXML_Concepts/index.html >.

----

NSXML supports many cool things:

*User data associated with XML elements and attributes
*NSValueTransformer
*DTD manipulation
*XML namespaces
*XQuery and XPath
*XSLT document transformations (wikified as XSLTransformations)
*XInclude
*CocoaBindings


In particular, XPath takes all the drudgery out of reading XML values.

