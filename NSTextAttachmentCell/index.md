---
layout: page
title: NSTextAttachmentCell
---

*See Also:* NSTextAttachment

A disambiguation: NSTextAttachmentCell is an NSCell subclass. <NSTextAttachmentCell> is a protocol.

**From the documentation:** The <NSTextAttachmentCell> protocol declares the interface for objects that draw text attachment icons and handle mouse events on their icons. With the exceptions of cellBaselineOffset, setAttachment:, and attachment, all of these methods are implemented by the NSCell class and described in that class specification.

----

Anyone know where to call highlight:withFrame:inView:?  I'd like my cell to draw highlighted when the user clicks it and was thinking this might be the method to implement to do so.  However, it never seems to be called.

