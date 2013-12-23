---
layout: page
title: OpeningURLFromCocoa
---

I'd like to simply open a URL (in the user's default web browser) from my Cocoa app, and I can't figure out how to do this.  I don't need to display the web page within my app, or anything like that.  Thanks.

----

See NSWorkspace.

----

Specifically, you can do:

[[NSWorkspace sharedWorkspace] openURL: [NSURL URLWithString: @"http://www.MyURL.com"]];

