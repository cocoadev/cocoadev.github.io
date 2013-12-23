---
layout: page
title: NSWindowController
---

NSWindowController is used to simplify the task of coordinating documents with complex view requirements by allowing each document to manage its own set of views.

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindowController_Class/Reference/Reference.html

http://developer.apple.com/documentation/Cocoa/Conceptual/WinPanel/Concepts/UsingWindowController.html

NSWindowController is usually used in conjunction with the documents in NSDocument-based apps. However, NSWindowController is convenient when creating other forms of window controllers, such as preference panels, custom about panels, and other windows. In particular, NSWindowController abstracts away all of the details when keeping track of windows and their delegates: making sure objects are disposed at the right time, lazily loading nib files, etc.

**See Also**

NSDocument and NSDocumentController

HowToReferToTheWindow

WhenDoISubclassNSWindowController

