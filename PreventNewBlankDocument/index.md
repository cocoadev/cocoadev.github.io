---
layout: page
title: PreventNewBlankDocument
---



Ok, this is probably a simple, no-brainer kind of thing and I promise I'll stop asking that kind of question soon. 

Anyway, my app is a doc-based, Core Data application. Naturally when you start it up without specifying a document to open, it opens up to an empty document.

In the context of this application, that's not helpful or beneficial in any way. In this app, you'll be wanting to open an existing data file, or explicitly create a new one. So, what I want to do is block the document from starting up. I'd prefer to have a menu-only startup, much like X-Code has. From there the user can File->Open an existing persistent data store or create a new one (rare) if they so choose.

For the life of me I can't find how to do this.

Any advice? Thanks.

-- chornbe ( ongoing Cocoa/Obj-C learning blog: http://learntomac.blogspot.com/2007/02/inaugural-post.html )

----

NSApplication's -applicationShouldOpenUntitledFile: is what you're looking for.

----

Thanks. I was subclassing. Adding that to a delegate did the trick. Thanks for the info.

----

Not sure what subclassing has to do with anything in this case, but ... congrats. :-)

---- 

I had the NSApplication class subclassed, and implemented -applicationShouldOpenUntitledFile: in that. It was never being called. Unwound the subclassing, wired up a delegate and implemented -applicationShouldOpenUntitledFile: in the delegate and it's working. Thanks again. --ch

