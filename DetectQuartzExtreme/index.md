---
layout: page
title: DetectQuartzExtreme
---

To detect whether a certain screen uses Quartz Extreme, get an NSScreen for that screen:

    
NSNumber* screenNum = theScreen deviceDescription] objectForKey: @"[[NSScreenNumber"];
BOOL supportsQuartzExtreme = CGDisplayUsesOpenGLAcceleration( (CGDirectDisplayID) [screenNum pointerValue] );


NB - can't test this code right now, so be prepared for compile errors if you copy & paste it. Also, not sure whether pointerValue does any other magic, if it does, try longValue or so instead.

Oh, and on older versions of MacOS X that don't have NSScreenNumber, you could mis-use the private _screenNumber instance variable. -- UliKusterer

