---
layout: page
title: MouseMovedWhenNotFocused
---

How do I receive mouseMoved: events in my NSTextView subclass when it is not the key view?  Currently, my mouseMoved:(NSEvent *)theEvent function only picks up mouse moved events when it is focused or key view, but I would like it to do so always.

- FranciscoTolmasky

----

Tell your view's window to accept mouse moved events, and get them there.

