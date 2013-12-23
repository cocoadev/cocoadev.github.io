---
layout: page
title: NSCursor
---

file:///Developer/Documentation/Cocoa/Conceptual/CursorMgmt/CursorMgmt.html http://www.professay.com/ http://www.scratchcardportal.com/
file:///Developer/ADC%20Reference%20Library/documentation/Cocoa/Conceptual/CursorMgmt/CursorMgmt.html (Panther/Xcode) http://rubbelloselotto.de/
http://developer.apple.com/documentation/Cocoa/Conceptual/CursorMgmt/CursorMgmt.html http://customessays.ws/ (ADC Web)
http://www.weddingspartiesanything.com.au/
[Topic]

Some details about     NSCursor that you may find useful:

*If you call     [NSCursor set] when your application is not active, the cursor will not change.
*If you call     [NSCursor set] twice (or more) in a row with the same cursor instance, the second (and subsequent) call will be ignored, **even if** the first call was ignored because your application was not active.


While writing a custom view that can be dragged to resize, the resizeLeftRightCursor gets replaces by the arrowCurser as soon as the dragging starts and does not get restored. I fixed it by using the Carbon     SetThemeCursor(kThemeResizeLeftRightCursor) function. I wonder how I should do it in Cocoa so that it works (-set / -push doesn't) or if it's even a bug.[https://www.failsafepayments.com/ 3d secure]

*To fix this, call -[NSWindow disableCursorRects] on your window when your drag begins and -[NSWindow enableCursorRects] when it ends.*

----
i'm trying to figure-out how to get a text-input cursor to be inverse when inside a dark background.
i'm using the 'white-on-black' theme in the terminal, and there the cursor shows-up white.
->
oops, solved this one: the terminal uses a custom cursor image-resource, which can be found inside the application package.
- arri

