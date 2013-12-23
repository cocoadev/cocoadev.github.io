---
layout: page
title: TrackPasteboardChanges
---

Hello i am trying to write a small netClipboard app with Rendevous.

Did anybody know a way to get informed when the General/[NSPasteboard generalPasteboard] gets changed.

Maybe there is a global Notification or something like that.
For now i am just polling it every .5 secs but thats not cool.


General/KarstenFuhrmann

Reference: General/FindPBoardNotify

----

Anyone have any ideas? *I believe the question is answered at the reference given above.*

----

PTH Pasteboard has clipboard history. I just tried to make 10 fast copies (using a macro in General/TextMate) and PTH Pasteboard only picked up 3 of the clippings. So this one does seem to use polling.

Though for Cocoa applications you probably could write an General/InputManager which does General/MethodSwizzling on the General/NSPasteboard class.
