---
layout: page
title: LearnOpenGL
---



I've scanned through some of the pages that mention OpenGL on CocoaDev.  None that I saw seem to have a good source for learning OpenGL under cocoa.  Cocoa doesn't really have glut (although I just built a glut) framework, and would like something that integrates with NSOpenGLView...  any recs?

Well, a quick google search turned up this: http://zerobyzero.ca/%7ektatters/index.html

As I'm going through this site, it seems a little threadbare in terms of textual explanations...

If you guys think of something better, let me know!

----

The NeHE tutorials are excellent and most OpenGL examples at the site have Cocoa versions.
http://nehe.gamedev.net/

----

If you are trying to learn OpenGL, I suggest creating an OpenGL screen saver, that way you don't have to worry about creating a full screen window etc.

If you haven't already, check out Apple's docs on OpenGL with Cocoa: http://developer.apple.com/documentation/Cocoa/Conceptual/OpenGL/index.html

There's quite a bit of sample code too:


*http://developer.apple.com/samplecode/GraphicsImaging/idxOpenGL-date.html
* http://developer.apple.com/samplecode/Cocoa_OpenGL/
* http://developer.apple.com/samplecode/Custom_Cocoa_OpenGL/
* http://developer.apple.com/samplecode/CubePuzzle/
* http://developer.apple.com/samplecode/NSOpenGL_Fullscreen/
* http://developer.apple.com/samplecode/OpenGL_Screensaver/


You may also want to try a search for GLUT or OpenGL at: http://cocoa.mamasam.com (a Cocoa mailing list)

One more thing: although they aren't Cocoa specific, TheOpenGLRedBook ( http://www.amazon.com/exec/obidos/ISBN=0321173481 ) and Blue Book ( http://www.amazon.com/exec/obidos/ISBN=032117383X ) are a must if you want to get a solid grasp on OpenGL and don't mind spending the money. These aren't technically beginner books, but think of them as the OpenGL User's Manual. If you only buy one, buy the Red Book.

If you're poor or don't want to spend the money on hard copies, older editions of the Red Book and Blue Book are available for free online at http://www.rush3d.com/reference/opengl-redbook-1.1/ and http://www.opengl.org/documentation/blue_book_1.0/ respectively.

