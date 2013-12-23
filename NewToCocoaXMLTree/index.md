---
layout: page
title: NewToCocoaXMLTree
---



Hi,

I just recently started using Cocoa and I was looking for some code examples of XMLTree in action.

I am trying to read in an XML file, modify it and write it out again.  not much to it but I am unsure of how to proceed or if XMLTree is the right class for the job.

----

As of 10.4, the built in NSXML is strong enough to do that.  See http://developer.apple.com/documentation/Cocoa/Conceptual/NSXML_Concepts/index.html

----

Thanks.  Does anyone know of code examples with file manipulation in them?  I find the class descriptions difficult to follow.

Thanks again.

----

try file:///Developer/Examples/Foundation/XMLBrowser

*Anyone else notice that in Safari, clicking on file:// URLs doesn't do anything, but if you enter it in the address field it opens?*

**Yeah, it's actually enough of a problem to be annoying (but not frustrating). Workaround: drag the link to the address bar. --JediKnil**

----

You can also use IconaraDOM, which is more like NSXML, but works on 10.2 and 10.3. --Theo

