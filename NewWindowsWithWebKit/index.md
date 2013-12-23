---
layout: page
title: NewWindowsWithWebKit
---



I'm having trouble with WebView's createWebViewWithRequest:. I've implemented all needed code in my project, as well as set the delegate for createWebViewWithRequest in WindowControllerDidLoadNib, but it won't yield new windows when asked to (JavaScript requests, "Open in New Window" requests) in the compiled application. I've even attached an NSLog to it - NSLog reports nothing as if the new window implementation were working perfectly.

What's the problem?

 The code is copied and pasted directly from the Developer Documentation.

