---
layout: page
title: NSXMLElement
---

Instances of the NSXMLElement class represent element nodes in an XML tree structure. 
An NSXMLElement object may have child nodes, specifically comment nodes, processing-instruction nodes, text nodes, and other NSXMLElement nodes. 
It may also have attribute nodes and namespace nodes associated with it (however, namespace and attribute nodes are not considered children). 
Any attempt to add a NSXMLDocument node, NSXMLDTD node, namespace node, or attribute node as a child raises an exception. 
If you add a child node to an NSXMLElement object and that child already has a parent, NSXMLElement raises an exception; the child must be detached or copied first.

