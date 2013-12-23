---
layout: page
title: RefernceToAnObjectInMyNib
---

Hi
I have the following problem: From a Core Data class I want to access an object in MyDocument.nib.
Since I'm new to Coca the only thing I could figure out so far is to use something like this:

id doc = [[NSDocumentController sharedDocumentController] currentDocument];

with the object I want to use being an IBOutlet in my subclass of NSDocument (I added public accessors).
But that fails because in the above statement nil is retourned.

Sorry for a question that i assume to be very simple ... but I'm new to Cocoa.
Thanks for help in advance

