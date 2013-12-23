---
layout: page
title: NSOpenGLPixelFormat
---



A object which provides drawing state information to the NSOpenGLContext class. By providing a null-terminated array of pixel format attributes, you can set or toggle features such as colour depth, depth sorting, and double buffering -- features which require the cooperation of the operating system in addition to any related settings for the OpenGL state machine.

Example:

 NSOpenGLPixelFormatAttribute attr[] = 
 {
     NSOpenGLPFADoubleBuffer,
     NSOpenGLPFAAccelerated,
     NSOpenGLPFAColorSize, 32,
     NSOpenGLPFADepthSize, 16,
     0 };
 NSOpenGLPixelFormat *format = [[NSOpenGLPixelFormat alloc] initWithAttributes:attr];
 glContext = [[NSOpenGLContext alloc] initWithFormat: format shareContext: nil];
 [glContext makeCurrentContext];

--BrentGulanowski

