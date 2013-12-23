---
layout: page
title: NSDocumentNotAlwaysMarkedAsEdited
---

I use the standard NSDocument class and [NSTextView breakUndoCoalescing] does solve a lot.

But when running my application in Tiger (10.4.11) my document isn't marked as edited in this case (dot in the red led top left):


*
 I type a character - document marked as edited; (ok)
*
 I save - document not marked as edited; (ok)
*
 I hit command-Z (undo) the document is marked as edited; (ok)
*
 I type a character - the document isn't marked as edited anymore. (wrong)


In Leopard this isn't the case, everything is working as expected. It is clearly a bug in Tiger but I wonder how I can fix this.
Whenever my document is changed, it should be marked so. My application needs to know it so that the document is saved before it is used to process.

