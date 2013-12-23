---
layout: page
title: StrangeWindowError
---

I am getting a weird crasher which is apparently caused by something in a subclass of General/NSWindow. The crash dump gives me 508 lines of the same message:

    
0   com.apple.General/AppKit  	0x92e2aad0 -General/[NSWindow validRequestorForSendType:returnType:] + 0x10
1   com.apple.General/AppKit  	0x92e2ab58 -General/[NSWindow validRequestorForSendType:returnType:] + 0x98
2   com.apple.General/AppKit  	0x92e2ab58 -General/[NSWindow validRequestorForSendType:returnType:] + 0x98
....
506 com.apple.General/AppKit  	0x92e2ab58 -General/[NSWindow validRequestorForSendType:returnType:] + 0x98
507 com.apple.General/AppKit  	0x92e2ab58 -General/[NSWindow validRequestorForSendType:returnType:] + 0x98
508 com.apple.General/AppKit  	0x92e2ab58 -General/[NSWindow validRequestorForSendType:returnType:] + 0x98


Does anyone have any idea what would cause this?

UPDATE:

This happens when the window is key and the menu bar is clicked.

Jacob

Are you sure that you aren't dealing with recursive calls, like:

- (void)myMethod
{
 ... do something...
 [self myMethod]; // Should be 'super' not 'self'
}

-- JP

Could be in a validateMenuItem: method call. -General/RossDude
