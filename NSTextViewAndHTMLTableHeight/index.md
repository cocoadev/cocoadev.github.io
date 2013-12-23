---
layout: page
title: NSTextViewAndHTMLTableHeight
---



Hello everyone!

I need to draw a table so I thought I'll make it through NSTextView and its ability to display HTML.

So I created text view in IB and wrote a piece of code:

    NSString *temp = @"<html><table width=\"100\" height=\"100\" border=\"1\"><tr><td></td><td></td></tr></table></html>";
NSData *htmlData = [temp dataUsingEncoding:NSUTF8StringEncoding];
and bound htmlData to my text view.

The table is drawn but it ignores the table's height argument. (width is ok)
What am I doing wrong?
Thank you in advace for any help.

----

First of all, you would probably be better off using WebKit than an NSTextView to do anything HTML. It's simply a lot better, and renders a lot closer to the standards. A quick test of the exact code you used in the NSTextView shows me that it works fine in WebKit (the table is the right height).

Second, this really isn't the forum to discuss HTML problems. I felt like being an ass and throwing that in there.

----

*First of all, ...*
Thank you very much! I'll try it!

*Second, ...*
True. But the problem was NSTextView, not the actual HTML.

----

Btw., how did you bind htmlData to WebKit (WebView)?

