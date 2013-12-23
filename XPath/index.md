---
layout: page
title: XPath
---

General/XPath is a sort of query language for XML. You can access any node within a document with a simple expression. The shorthand form looks much like a UNIX file path.


Consider this simple XML-document
    
<persons>
    <person id="811201">
        <name>Theo</name>
        <surname>Hultberg</surname>
     </person>
</persons>


<code>/persons/person[1]</code>
retrieves the first person-element that is a child of the root node ("/" is the root node).

<code>/persons/person[@id="811201"]</code>
retrieves all person-elements that are children of the root element and has an attribute ("@<name>") with the name "id" that has the value "811201".

<code>//name</code>
retrieves all name-elements in the document

<code>//person/@id</code>
retrieves all id attributes of person-elements in the document.

<code>/persons//name</code>
retrieves all name-elements in the document that are descendants of person-elements (that are children of the root node)

Double-quotes and single-quotes are equivalent.

See http://www.w3.org/TR/xpath for more info.

-- General/TheoHultberg/Iconara

----

General/IconaraDOM has basic General/XPath-support for working with XML-documents in Cocoa. General/XMLTree has some General/XPath-support, too.

Tiger introduces the General/NSXml classes, which include General/XPath support.
