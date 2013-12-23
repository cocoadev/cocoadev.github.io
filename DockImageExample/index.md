---
layout: page
title: DockImageExample
---

It's really simple to manipulate the image that show up in the Dock.
In your AppKit application you only need to call :

[NSApp setApplicationIconImage:anImage];

Usually you create your image by doing :

anImage = [NSImage imageNamed:@"image.jpg"];

(of course the image should be part of your project)

You can find a good example including the source for a mini project on the web site :
http://www.funcall.com/Download/DockImage.html

