---
layout: page
title: RefactoringInCocoa
---

in CocoaMostWanted, the topic of adding automatic refactoring support was mentioned.

This page is a place to discuss refactoring in cocoa/obj-C.

Refactoring itself is covered really well at the Portland Pattern Repository Wiki:
http://www.c2.com/cgi/wiki?WhatIsRefactoring,
And lists of refactorings are to be found at:
http://www.c2.com/cgi/wiki?RefactoringLanguage
and at http://www.refactoring.com/catalog/

The FreeOnlineDictionaryOfComputing also has an entry:
http://foldoc.doc.ic.ac.uk/foldoc/foldoc.cgi?query=refactor&action=Search

*Disclaimer: I'm neither a refactoring expert nor an old hand at ObjC, so some of these topics might be mislabeled - please add corrections! -MichaelMcCracken *

*It'd be great if some of the more experienced cocoa/objC developers could add to this, to educate the rest of us.*
----

Because of objective C's smalltalk heritage, and the connection between refactoring and smalltalk, it makes sense that many refactoring transformations can be done easily in objective C. 

Are there any ones that are specific to objective C, or even better, specific to idioms in Cocoa?

Here are some ideas:
Some of these might be hard to do automatically, but of course refactorings don't need to be automatic!


* **Switch between notifications and direct method calls**

*It seems like a tradeoff between flexibility and readability - when to use notifications, if you can access the listening object directly?*

* **Extract Category**

* A class has a group of methods that you would rather split into a category*

* **Inline Category**

* Categories may not be good for understanding and maintaining large projects - so you might want to merge a class and a category.
*

* **Make Conform to Protocol**

* You need to make your object conform to a protocol. What were all those method signatures again?*

* **Make Class Into Class Cluster**

* You may realize partway through development that your class would really be a lot more flexible and useful (and your interface less cluttered) if it were a class cluster.*



Any More?

----

Does anyone know a good refactoring tool for c/obj-c for macosx?
If there were any running as PB plug-ins it would be great!
It seems that all refactoring tools around are for java only :(
Thx -- peacha

See the ObjectiveCeeRefactoringProject.

