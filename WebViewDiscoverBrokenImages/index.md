---
layout: page
title: WebViewDiscoverBrokenImages
---

I am using they security spy application (www.securityspy.com) that pushes live video to my browser using 'server-push stream'. The software author describes this as quote: "One connection to the server is opened and a stream of live images is continuously transmitted and displayed in the browser. ". This works great for me and I stream these images to a General/WebView in a cocoa application I have created. However, if the network connection is disrupted for any reason then the General/WebView shows the blue box with a question mark indicating a broken or missing image and remains in this state. At this point I would like to be able to detect that the live images have stopped being transmitted and refresh the browser to try to recapture the stream. The way it seems to make sense is to approach this problem is to try to determine using either an event raised by the General/WebView, or polling it on an General/NSTimer is to try to discover when the image is broken and then trigger a General/WebView refresh. It would be great if I could have the General/WebView tell me about the problem by raising some event rather than have to poll.

Does anyone know how to determine if there are broken image links in General/WebView? Any help (or suggestions for another approach) would be greatly appreciated!

The web page that is being processed by webview looks like the following:

<html><head><title>Live Video</title><style type="text/css">
<!--
p {font-family: Verdana, Arial, Helvetica, sans-serif; font-size: 12px}
-->
</style>
<script language=javascript>function General/OpenPTZWindow(cameraNum) {
window.open("++ptz/controls?cameraNum="+cameraNum,"PTZ"+cameraNum,"toolbar=0,scrollbars=0,location=0,statusbar=0,menubar=0,resizable=0,width=230,height=190");
}</script></head><body>
<p align="center"><b><font size="3">Live Video</font></b></p>
<div align="center">
<img src="++video?cameraNum=0&45695" width=640 height=480 alt="Video">
<form name="form2"><p></p></form></div></body></html>

----
Check out the General/WebResourceLoadDelegate protocol. You can use that to find out when a load fails, and take the appropriate action.

----
Thanks for your reply. I tried the different General/WebResourceLoadDelegate protocol methods and was really hoping that didFailLoadingWithError: would have worked for me.  However when the connection is broken and the bad image is displayed nothing is called. I am trying to capture everything I can find in hopes of getting a call I can react to
    [browser setFrameLoadDelegate:self];
    [browser setUIDelegate:self];
    [browser setResourceLoadDelegate:self];

When I trace the methods getting called the page immediately completes with 'didFinishLoadingFromDataSource' while the live video keeps streaming. I have reduced the HTML down to quite simply: <img src="http://localhost:8000/++video?cameraNum=0&30489" width=640 height=480 alt="Video">  I just need to know when this fails.
