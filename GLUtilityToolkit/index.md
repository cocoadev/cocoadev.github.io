---
layout: page
title: GLUtilityToolkit
---



GLUT; the OpenGL Utility Toolkit. A generally useful set of APIs for CrossPlatform coding. They're not going to be useful in all situations or for all people; they don't provide as much functionality as you might sometimes want because they have to be implemented on top of several different windowing models. However, if you need cross-platform, easy code, and you don't mind not having standard UI items like buttons to play with (if you want 'em, you're probably writing them yourself, basically), it might be good for you.

RobRix has been using it heavily as the basis for some CrossPlatform frameworks.

In MacOSX, GLUT is based on Cocoa. Note that there's a bug in the glutTimerFunc() in the source (as found on the Apple sample code page, as of September 28th), so you should probably just go with the J**'agWire version and forget the sample code.

Remember, it's not for everything, but it's a tool, and if you can use it to your advantage, fantastic!

