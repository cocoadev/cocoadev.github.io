---
layout: page
title: OpenGL
---




See http://www.opengl.org/ for news and information regarding OpenGL.

What is OpenGL? (from http://www.opengl.org/about/overview.html )

OpenGL is a cross-platform standard for 3D rendering and 3D hardware acceleration. The software runtime library ships with all MacOSX, Unix, Linux and Windows systems.

There is some info on OpenGL on various platforms at NeHe, including OS X (using GLUT, whatever that is) (GLUT is the OpenGL Utility Toolbox):

http://nehe.gamedev.net/opengl.asp

Some GLUT examples exist on the system in    /Developer/Examples/GLUTExamples

 ----

Apple has an NSOpenGLView Teapot example on their web site (he said he found it by searching Google).  Also, there is source to a set of OpenGL screensavers on EpicWare's site.

-- StevenFrank

----

Yeah, the EpicWare code isn't great example code. Eric just basically got them working and left them alone. Looking through the code is fine (that's what it's there for), but don't take it as gospel. 

Here are some tips I've developed over the past year for working with NSOpenGLView.


* Don't bother with locking/unlocking focus on the view to do drawing. It doesn't seem to do any good.
* When doing drawing begin with glView openGLContext] makeCurrentContext] and finish with [[glView openGLContext] flushBuffer]. In particular, use flushBuffer instead of glFlush(). glFlush works with single buffer only, but flushBuffer will work with both single and double buffers. I find myself caching the value of [glView openGLContext] in my screen savers for convenience.
* If you need to set a pixel format, make sure the pixel format is set before you call [glView openGLContext] the first time. 
* You cannot safely issue GL commands into the same [[NSOpenGLContext from two different threads at the same time. You must use a lock or otherwise guarantee only one thread at a time is in your relevant code.
* You can safeuly issue GL commands into separate NSOpenGLContexts from separate threads. You can use this to load textures into one context on one thread and display them on another context on another thread.


Here's a function I use to report errors in GL. Call it after every gl function call with some descriptive text (say the name of the function you just called).

    
__private_extern__ void CheckGLError(const char *note)
{
    GLenum error = glGetError();
    if (error) {
        NSLog(@"%s [%d]: %s", note, error, gluErrorString(error));
    }
}


-- MikeTrent

----

See also FadeFromBlack

-- MikeTrent

----

By the way, Apple's updated all of their own information regarding Cocoa and OpenGL...seems good so far!

http://developer.apple.com/techpubs/macosx/Cocoa/TasksAndConcepts/ProgrammingTopics/OpenGL/index.html

-- RobRix

----
GNU 3DKit  http://www.fsf.org/software/gnu3dkit/gnu3dkit.html

This little project is looking really good, but it's the work of only one man! It is an open source 3D mathematics and scene graph API moving toward alpha release 1.4. I would dearly love to see this project succeed really big.
----
Quesa http://www.quesa.org

OK, technically this is a Carbon system, but QuesaCocoa now works with NVidia cards in Jaguar, meaning it is effectively useable with your NSView subclass. If you programmed with QuickDraw3D in the past, you should check this out. Very mature and very powerful. Too bad it's not in Objective-C.

----

Other OpenGL pages on CocoaDev:
[Topic]

