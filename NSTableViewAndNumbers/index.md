---
layout: page
title: NSTableViewAndNumbers
---


Hi. I have one question about Cocoa and Core Data. 

How can I sum numbers in NSTableView and show them in NSTextFiled? Like in this pic:

http://shrani.si/files/picture2qc7m.png
----

Assuming you want a bindings answer, the key path should look like `dataSource.arrayName.@sum`.

More information at http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/Concepts/ArrayOperators.html

-G

----

This post begs for a HowToAskQuestions link, but I guess somebody already answered.

