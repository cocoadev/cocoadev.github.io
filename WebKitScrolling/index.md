---
layout: page
title: WebKitScrolling
---

Is there any way to force a General/WebView NOT to embed itself in a General/ScrollView when the window is smaller than the General/WebView?  I have a General/WebView based program that displays content of a fixed size (760x420).  If the window containing the General/WebView of this size is not at least 16 pixels taller and wider (776x436), the General/WebView turns into a General/ScrollView.  With this increased border size, small margins of the background color appear around the content.  I'd prefer to have the borders of the window be exactly the same as the borders of the content (760x42).  Is there any way to achieve this? --General/OwenAnderson


You could just make the webview, then extract it using the docmentView message

myRealWebview= [myWebViewWithinAScrollView documentView];

----

General/WebView's don't respond to "documentView" --zootbobbalu
