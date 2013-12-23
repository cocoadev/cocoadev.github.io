---
layout: page
title: NSDateFormatterandNSTextView
---

I have an NSTextView that I want to add an NSDateFormatter to, but as far as I can see, in IB, you can only add date formatter to an NSTextField. Is there a way to add a date formatter to a text view in Interace Builder, and if there isn't, what would be the best way to do it programmatically?

Thanks in advance.

----

Only an NSCell can take a formatter, only an NSControl has cells, and NSTextView is not a control.  So you cannot add a formatter to an NSTextView.

----

OK, thanks.

