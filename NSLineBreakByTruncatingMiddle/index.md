---
layout: page
title: NSLineBreakByTruncatingMiddle
---



Check out this nice example:
http://developer.apple.com/documentation/Cocoa/Conceptual/Rulers/Tasks/TruncatingStrings.html

----

See NSParagraphStyle.h

    NSLineBreakByTruncatingMiddle	/* Truncate middle of line:  "ab...yz" ??? Doesn't work yet */

For a work-around, see BetterTruncatingStringsInTableView.

----

----
Thanks! This works awesome!

----

Here is a sample project showing NSLineBreakByTruncating. This example is based on the Apple code at the address above.

http://www.nancesoftware.com/development/sample_code/linebreakbytruncating/

I added the option to truncate by using NSLineBreakByTruncatingHead, NSLineBreakByTruncatingMiddle, or NSLineBreakByTruncatingTail.

JacobHazelgrove

