---
layout: page
title: NotSavingRemovedItems
---

Okay, here's my problem. I have an NSArrayController that contains a list of objects. My app is document based and uses CoreData and Bindings. The app works fine when I add objects, but when I remove them, they seem to remove, but when I save the document and re-open it, they are back! I'm guessing that the data store in the file isn't getting updated, but why would it still be adding objects fine, but not removing them? Any ideas? Thanks.

*There's not really enough information here. How is your remove implemented? Is it just a direct call to the NSArrayController's     remove: action?*

Yeah, it calls remove:sender. I think I may have more info, I tried changing my data store from SQLite to Binary, and it works now. It also works for XML. Could this be a problem with SQL itself or maybe something I've implemented?

