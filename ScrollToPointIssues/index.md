---
layout: page
title: ScrollToPointIssues
---

Hello mom,
I have been having some trouble on 10.4.1 with NSClipView scrollToPoint method. I have a custom NSClipView subclass which intercepts scrollToPoint, prints a message to devlog and then calls a delegate method. It is used with a document view whose size is frequently changing. Basically I have an overview which you can drag a rectangle in, and the document view is resized so that the corresponding region fills my scroll view, so far so good, but of course I want to scroll to the origin of this selection, and that is when problems arise. Basically everything works as planned without the scrollToPoint method called, but once I call it, BOOM!, only ONE invocation of scrollToPoint is called, the scroll view is NOT updated, and all further scrollbar movement is ignored, ie no NSClipView methods get called. This unfourtunate scenario only occurs until I resize the doc view again. And yes the parameter to scrollToPoint is perfectly sane.

What makes this particularily vexing is that my code worked immaculately under 10.2 and 10.3. I am working on a test program to figure this out, just thought I would let some cocoa guru's have a crack while I sleep off my latest coding binge. -JJJ

