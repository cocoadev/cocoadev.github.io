---
layout: page
title: OpenGLTiling
---

Can anyone help me turn this into objective c/cocoa?
Thanks!
http://www.gamedev.net/reference/articles/article1256.asp

----

Yes, have a look at the NSOpenGLView documentation, copy one of the simple Apple sample code examples for NSOpenGLView and use your cut n paste function....

http://developer.apple.com/samplecode/Custom_Cocoa_OpenGL/Custom_Cocoa_OpenGL.html

The draw_tiles function code should be placed in the drawRect method of your NSOpenGLView custom class.

