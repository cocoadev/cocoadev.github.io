---
layout: page
title: SuddenMemoryLeak
---

A good-behaving app (doc-based) I working on develop all sudden, awful memory leak.
So far I have trace it to see that dealloc method in window controller does not called,
so no objects I  do now create in window controller will go away when close the doc.
I am not far along this, so I probably just begin from start, but wish I know how I go wrong.
Can anyone suggest what might caused this awful bug? I am still very new at this and with no clue.

----

Are these objects instantiated in a nib?

----

Window controller show in nib; other objects creates from init of controller.

the WC is *instantiated* in the nib? as opposed to being General/FilesOwner?

----

No, I do the normal way - as Files Owner

----

Normally for a document based app the Document object should be the nib owner.  You're presumably expecting all the nib objects to be released for you, so you should be doing things the standard way.

----

Ok. I start again and put no winodw controller. But why is telling me to sublass window contoller class if doc will have more one widnow?

*You would generally subclass it entirely programatically, and add the window controller to your document with its addWindowController: method (or whatever it's called)*

----

Actually, I deleted my comment above because I never write document based apps. :-)  You or someone else resurrected it.  Wait for someone who knows more about this.  However, if you have multiple windows in multiple bundles managed by your document then you should probably be using    the methods in General/NSDocument pertaining to window controllers.  My guess is that you are expecting your window controller to just go away on its own, and there is some stuff like that built into cocoa, but only if you stick closely to the design Apple provides.
