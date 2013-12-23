---
layout: page
title: ShowAndHideNSPopUpButton
---

Is there any method to show/hide an NSPopUpButton like the "Hidden" checkbox in Interface Builder does?

----

    -[NSView setHidden:].  NSPopUpButton is a subclass of NSView.

----

Thanks. I missed that when looking through the docs.

