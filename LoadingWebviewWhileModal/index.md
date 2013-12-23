---
layout: page
title: LoadingWebviewWhileModal
---

I noticed that loading in a WebView does not work when the application is running a modal, is this a bug or is it supposed to be that way? And is there a way to force the WebView to load even while the application is running a modal? Thanks in advance, PabloGomez

I just found the answer to my question. Apparently NSURLConnection does not work while not in the main run loop. It seems to be a known issue, but if anyone has any workarounds please let me know. PabloGomez

Just found a discussion about this at http://lists.apple.com/archives/Webkitsdk-dev/2004/Jun/threads.html#00034 there's a hack there if anyone's interested.

I've just implemented a workaround (a bit of a hack and a bit complicated but it works). Here's what I did: create a sub class of NSURLConnection, use poseAsClass:, catch initWithRequest:delegate: and run it in a worker thread. Catch all subsequent method calls to NSURLConnection by using a proxy and catch all delegate callbacks and forward them back to the main thread when they occur. Note that some of the NSURLConnection delegate callbacks aren't run on the main thread (such as connection:willCacheResponse:). I paused the main thread whenever the run loop in the worker thread was about to wake (using a CFRunLoopObserver) to ensure that URLConnection ran synchronously with the main thread. Chris Suter, Coriolis Systems

