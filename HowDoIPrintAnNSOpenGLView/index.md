---
layout: page
title: HowDoIPrintAnNSOpenGLView
---

**Question: How do I print an NSOpenGLView ?**

**Answer:**
OpenGL is NOT device independent.  OpenGL drawing always effects a pixel buffer/bitmap.  There is no easy or straight forward way to translate OpenGL drawing into vector commands to a printer or any other non-pixel-buffer type of device. 

There are several options:

1) Use OpenGL to draw into a bitmap of suitable size/resolution and just print the bitmap.

2) Convert OpenGL drawing commands into device independent drawing commands like PDF, Postscript, or Quartz 2D.  However, the printed output will not look exactly like the screen version [no WYSIWYG]

3) Write your own drawing API that uses OpenGL for pixel buffer drawing and something else for all other devices.

4) Use someone else's API that uses OpenGL for pixel buffer drawing and something else for all other devices.

I always say it like this: 

- OpenGL worships speed over quality in all cases [even to the extent that OpenGL is sometimes useless due to poor quality].  Modern GL hardware is designed to work with only very specific pixel buffers, and the basic design of OpenGL is contrary to device independence.  

- Quartz2D worships quality over speed in all cases [even to the extent that Quartz2D is sometimes useless due to poor speed]. Quartz2D is hardware accelerated only in specific circumstances such as rendering to a low resolution pixel buffer, and Quartz2D is so pure regarding device independence that many operations aren't optimized.

I have never seen very satisfactory printed output from OpenGL particularly when alpha channels, lighting, fog, stencil buffers, etc. are used.  It is hard to do better than a screen shot printed at an appropriate DPI.  glReadPixels() can be used to get the pixels buffer contents from an NSOpenGLContext.

