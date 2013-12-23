---
layout: page
title: NSNibInitWithContentsOfURL
---

Is there a use for NSNib's -(id) initWIthContentsOfURL, and if so are there security concerns? I can't really think of a use for it.

----

Aren't you now supposed to use URLs for referring to local files in some things now? Perhaps that's it's intended use.

----
Yes indeed, a great many APIs use URLs for local files just for... fun, or consistency. CoreFoundation in particular loves dealing with URLs instead of paths.

