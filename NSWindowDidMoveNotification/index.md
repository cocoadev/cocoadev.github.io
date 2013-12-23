---
layout: page
title: NSWindowDidMoveNotification
---

NSWindowDidMoveNotification is an NSNotification object posted by an NSWindow when the position of its frame changed due to the user dragging it. However, for windows with title bar, i.e. [window styleMask] does not match NSBorderlessWindowMask, the NSWindowDidMoveNotification is only posted upon drag end or pausing of dragging for, I would guess half a second or more. 

If you want to respond to the change of a window's position in a live fashion, you need to actively watch the dragging operation. I have prepared sample code for a category on NSWindow that makes windows post the NSWindowDidMoveNotification continuously upon dragging: http://web.mac.com/mabi99/iWeb/marcocoa/blog/012ABB7D-DCCE-4336-ADB8-336C88DE5BE5.html

--MarCocoa

