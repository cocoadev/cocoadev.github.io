---
layout: page
title: NSAtomicStore
---

So, Leopard includes a lovely new class, NSAtomicStore. I've read through all the documentation and know what need to do to populate Core Data from my custom file format. Except, I have no idea where to start. I've created my subclass of NSAtomicStore; how do I get the framework to use it? I've done my call to      + (void)registerStoreClass:(Class)storeClass forStoreType:(NSString *)storeType  but my code is never called when a Untitled document is being created.

Does anyone have a sample they can post? Or perhaps a quick anecdote about that last tricky step that had them going for a while?

----

As seen on: CoreDataWish

*

Leopard will have new API that allows you to override the default data storage method. The Leopard dev samples even contain code showing how this is done.

*

Does anyone have that laying around somewhere?

----

Me again. I may have found what I was looking for here: http://developer.apple.com/samplecode/HTMLStore/

