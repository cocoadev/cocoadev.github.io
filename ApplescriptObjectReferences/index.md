---
layout: page
title: ApplescriptObjectReferences
---

Hi All,

I've come across something of an architectural problem whilst getting started in Applescript.  I'm doing a basic document-based application.  The NSDocument subclass (MyDocument) contains a sub-object (Prefs), which contains a few values:

App -> MyDocument -> Prefs -> Value I want

What i'm trying to do is have the "Value I Want" be a property of the document class in Applescript.  IE, I could say 'set value i want of document 1 to "asdf"', and the value would actually go into the Prefs object.  Is this possible somehow?

----

Of course. You can add KVC accessors to the document class to forward those requests to the prefs object. If you don't want to explicitly list them in the document class, simply implement     -valueForUndefinedKey:,     -setValue:forUndefinedKey:, and     -handleTakeValue:forUnboundKey: to forward any unrecognized keys to the prefs object. Although     -handleTakeValue:forUnboundKey: is deprecated, it is what CocoaScripting uses instead of     -setValue:forUndefinedKey:.

--DustinVoss

