---
layout: page
title: DocumentBasedAnnoyances
---



I have a couple questions about document-based apps. The first one is, how do I disable the document dirty state? I really don't need it in my app, and it's annoying to have the "Do you want to save?" sheet presented every time the user closes the window.

Second, my document-based app has been experiencing occasional unexpected quits for seemingly no reason whatsoever. I was told that this is a bug with document-based apps and can be fixed. Does anybody know more about this? Google turns up nothing on the subject.

Any help is appreciated, and I apologize if these are n00b-ish questions. Thanks!

*Please PostYourCode for the second question, at least any that's specific to your document. For the first, one way to disable the dirty state is to override NSDocument's     isDocumentEdited: to always return     NO (warning: untested). --JediKnil*

----

Thanks for the answer on the first part! I'll get to testing it right away.

I don't have any code for the document part, as the program is a web browser. I am using the document-based format for its multiple window management and new window capabilities.

----

Programs shouldn't "quit for no reason", document or not. Check the exit return code in console, and check it in the debugger (now is a great time to learn it if you haven't yet.) Most of the time this behavior is caused by an error in memory management, so make sure you're retaining objects when you should be.

Also, if you're not using an NSDocument subclass, you probably shouldn't be using a Document based project-- you can still use NSWindowController and multiple windows in a standard cocoa project.

*That's not exactly true -- Safari, among other programs, uses NSDocument to implement multiple windows, etc. The key word above is "subclass" -- you should have **some** code in a subclass of NSDocument. --JediKnil*

