---
layout: page
title: TextViewIsScrolledNotification
---



Is there any way to be notified when a textView is scrolled by user?

----

An General/NSTextView in IB is actually an General/NSTextView inside an General/NSClipView inside an General/NSScrollView. Either the General/NSTextView or the General/NSClipView will be changing their bounds or frame during a scrolling operation; most likely the General/NSClipView will change its bounds. You can have any General/NSView send an General/NSNotification when its bounds or frame are changed.
