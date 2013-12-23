---
layout: page
title: CocoTron
---

Cocotron (not CocoTron) is a clean-room partial reimplementation of the Foundation and AppKit frameworks of Cocoa. Unlike Cocoa, it's cross-platform (Foundation works on Linux, Solaris and Windows and AppKit works on Windows). Unlike similar OpenStep-inspired projects, such as GNUstep, Cocotron does not require installation and on Win32 the basic frameworks can live embedded inside the application bundle, making it a more lightweight choice.

Cocotron unfortunately is not yet a complete implementation of OpenStep; it's still under development (open source under the MIT license) at http://www.cocotron.org/.
----
Congratulations.  What you have done looks good to me so far.  I only wish you had consulted with me, Erik Buck, sooner because you duplicated a lot of work that I have already done :(.
I predict that reasonable text support and rendering will be the hard part going forward.  At least, I have not been able to get very far.  There is essentially nothing useful on Linux or Solaris (particularly if you don't want to be infected by GPL).

With the release of Windows Vista, you can leverage Vista's almost exact clone of Quartz 2D APIs to provide anialiased vector graphics, transparency and compositing, path operators, etc.  You may also be able to use Vista's text rendering under the surface.  Of course, the drawback to using any Windows/Vista technologies to implement CocoTron is the lack of cross platform support.  In other words, whatever class implementations you use on Windows, you will probably need completely different implementations on Linux or Solaris.  This is beyond the occasional #ifdef.

NSOpenGL classes will be no technical problem on Windows.  I use OpenGL as the implementation for all graphics on Windows now.  You will have problems getting applications that contain a lot of OpenGL source code to port because the OpenGL standard is still very weak.  Everybody uses incompatible extensions and assumptions.  It is unusual for the same non-trivial OpenGL program to work the same way with two different graphics cards on the same platform let alone different platforms.  OpenGL is also very poorly suited to tasks like vector text rendering.  OpenGL is NOT device independent.  OpenGL sacrifices output quality in favor of speed in all cases.  Quartz 2D is device independent.  Quartz 2D sacrifices speed for output quality in all cases.  The biggest obstacle of all is that there is no good way to print OpenGL vector graphics.  For all of these reasons, you may want to implement Quartz 2D (or Display Postscript/PDF) type features using Vista technology or I suppose libArt on linux if you can withstand contact with GPL.

----
As of July 2007, does Cairo offer sufficient graphics and text support? http://cairographics.org/

I note that the Cairo home page says it is offered under dual GPL and MPL licenses but am not sure if you can use pure MPL all the way as I thought it or parts (Pango?) were only LGPL, relying on GTK+ which is only LGPL.

----
My I ask why you implemented your own ObjC runtime from scratch rather than port Apple's runtime? --OfriWolfus
----
**Quartz2D now implemented in Cocotron**

The project now includes an almost from scratch MIT licensed rasterizer which is designed to implement all of Quartz2D in software. It is the default rasterizer and has been used in some commercial projects already. It implements anti-aliasing, gradients, alpha blending, patterns, variety of pixels formats, PDF display, and so on. It is not complete but does most common functions and is relatively easy to work on and extend to completion as people need features. It does use GDI or FreeType for font rendering but aside from that is an all encompassing system. 


----
How do you change the title? The T is not supposed to be capitalized in "Cocotron". Anyway ...

Erik, I was as unaware of your work as you were of mine. Is your work publicly available? It is a problem for me to depend on others people work which I can not use :) Perhaps you could contribute some of it to Cocotron?

CoreGraphics definitely is a challenge. GDI on Windows, which Cocotron uses now, is sufficient for a lot of stuff and gives you printing almost free. GDI+ is closer to CoreGraphics in functionality than GDI and printing will work, but it is still not cross-platform and I am trying to avoid the use of single platform API's. GDI+ is in XP/Vista and a redistributable DLL for Me/2000. An OpenGL based renderer is probably a really good answer for screen display and if someone wants to work on it I'd be happy to have it. On Windows either way you need a GDI/GDI+ based renderer for print output. Cairo is obviously similar but they have changed the licensing twice now (BSD -> LGPL -> LGPL/MPL), which I find unreliable. Cairo is also different enough (and may diverge more) from CoreGraphics to pose problems at some point. It makes more sense on Unix where you don't have something like GDI+, but for Windows, layering CoreGraphics on top of Cairo on top of GDI becomes excessive. I'd rather expend the energy on a ground up implementation than battling differences. There is a lot of activity in the vector-graphics-on-OpenGL space right now and perhaps a new project will come up that can be used. There is plenty that can be done until that happens. 

To avoid any confusion with the above, Cocotron layers the GDI renderer with a CoreGraphics API and would for other renderers. The goal is full source compatibility.

OfriWolfus, I have been working on it since before Apple had an open source runtime. The APSL 1.0 was very unfriendly and the APSL 2.0 is not very pleasant either. I felt is was more beneficial to have a ground up implementation under the MIT license.

-ChristopherLloyd

----
The wiki depends on having some CamelCase to identify words that can be linked. CocoTron will be automatically linkified, but Cocotron is just a regular word, hence the miscapitalization. It's a common thing to do on wikis like this. -- MikeAsh

----

Thanks Mike, I hereby declare that TheCocotron is the official way to CamelCase Cocotron.

-ChristopherLloyd

----

*CoreGraphics definitely is a challenge*. Have you checked out Anti-Grain Geometry? It's a cross-platform Quartz-like graphics system - in many respects more advanced than Quartz and very powerful. http://www.antigrain.com/  I have no connection with the code but it looks god to me. -- GrahamCox

----
The problem with AGG is that the current version is GPL, not even LGPL. The output looks great though.

-ChristopherLloyd


----

"CocoTron" will do. Please don't create another page. :-)

----

From the GPL: 
"A compilation of a covered work with other separate and independent works, which are not by their nature extensions of the covered work, and which are not combined with it such as to form a larger program, in or on a volume of a storage or distribution medium, is called an �aggregate� if the compilation and its resulting copyright are not used to limit the access or legal rights of the compilation's users beyond what the individual works permit. Inclusion of a covered work in an aggregate does not cause this License to apply to the other parts of the aggregate."

It would seem unnecessary to avoid GPL components unless you were planning on profiting off of a derivation.  Utilizing a component with no modification should be a trivial non-concern.

----
Doesn't work that way. That whole business of an "aggregate" just means that, essentially, if you ship a collection of unrelated software, the fact that one piece of software is GPL does not mean the others have to be GPL. However, if Cocotron incorporated AGG or any other GPL software in order to implement functionality, it would *not* be an "aggregate" as defined above, but would rather become a derived work. Therefore Cocotron would then have to be licensed under the GPL, and in turn any application that used Cocotron would have to be GPLd.

----
 Only the new 2.5 release of AGG falls under the GPL. AGG 2.4 is still available under a less restrictive (i believe BSD License). See http://www.antigrain.com/license/index.html.

----
 Cocotron has recently added support for using AGG (2.4 or you can buy a commercial license) as an alternate plug-in renderer, the initial work was paid for and has been improved greatly by Plasq. So, there is a built-in renderer which is MIT licensed or you can use AGG if you want. Both of these will be supported going forward.

----

Two recent (2011,2012) tutorials:

http://lacquer.fi/pauli/blog/2012/02/win-win-with-cocotron-part-1/2/

http://blog.tlensing.org/2011/05/01/cocotron-and-xcode4-how-to-run-objective-c-apps-on-linux/

