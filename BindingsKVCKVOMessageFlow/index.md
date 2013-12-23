---
layout: page
title: BindingsKVCKVOMessageFlow
---

[Bindings Portion of the question moved to BindingsBehindTheScenes]

I still have this KVC/KVO question which I can't seem to find the specifics to: If I observe a keyPath, will I receive a notification when *any* object in the path is changed, and if so how is this internally supported (e.g. if I observe a.b.count and I change a (which thus changes count), do I find out?  and if I do does this mean that a/b/and count were all actually being observed?)

----

It may help to clarify your data structure a little bit more; the above description is terse to say the least.

----

It's not any particular data structure, its a general question about dealing with key paths.  Simply put, does changing any value in the key path change a value further up in the key path.

I'm observing "a.b.c" in object myObject.  if I now do [myObject setValue: Blah forKeyPath: "a.b"], will I get a notification that c changed.  Or, let's say I do [myObject setA: somethingElse], again, will I be notified about c changing?

----
The question is valid, and clear. And the answer would be nice to know!
I don't know the answer, but may I suggest you build a small test app to check it out?... and let us know!

----

Do you really think the theoretician who posed these questions is much interested in something as dirty as a test app? Or, for that matter, ever asks a question he does not already know the answer for?

----

You will be notified for the keys of the objects you are observing. No more, no less.

