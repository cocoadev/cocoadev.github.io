---
layout: page
title: ObjCAndOpenGLForAGameEngine
---



Hi, I come from a C# and Managed DirectX background, but I'd like to start using my Mac more. I've been playing with Objective C, and feel reasonably confident with the language, but I was wandering, how practical is it to program a game engine completly in Objective C? The type of work I do is experimental stuff - like trying out new terrian rendering methods, sky work, water rendering - not full games, but more graphics engines and problems. Would I better off using C/C++ (ignoring the speed!), or is Objective C good too? From what I've seen, Objective C looks to have it's main advantages with working with Cocoa and interfaces, so I'm not sure how much I'd benefit from using Objective C...

Hopefully a wiser one than I can shed some light :)
----
I use Objective-C with OpenGL extensively.  In fact, I do most of the things you are interested in.  Our software is for 3D terrain visualization, architectural 3D, and 3D editing environments.  There are areas where I am really using the C subset of Objective-C. For example, vertex arrays, shaders, most matrix math, etc. are all really in C.  However, we have Objective-C classes for terrain elevation management, texture loading and manipulation (particularly Core Image generated textures and Quartz Composer generated textures), the view frustum, camera, lights, quad trees, scene graphs, models, special effects, etc.

In my opinion, the same dynamic qualities that make Objective-C well suited for GUI development apply to 3D environment development too.  Objects in a scene may be loaded as plug-ins at runtime.  Many/most 3D applications require extensive user interaction that is not dissimilar from the interaction with 2D GUIs.  For example, moving a camera in a 3D world is kind of like moving a mouse in a 2D world.  Interactively selecting a 3D object or deforming some terrain in 3D are not that dissimilar to pressing a 2D button or resizing a 2D window.  Cocoa's responder chain, notifications, plug-ins, and view hierarchy are an elegant and productive way to build GUIs.  The exact same techniques enable elegant 3D world development.  The scene graph is the rough equivalent of the view hierarchy.  Notifications and the responder chain and plugins are similar.  In some ways, our application is a 3D incarnation of Interface Builder.  Interface Builder certainly needs the dynamism of Objective-C!

Many 3D environments suffer from inadequate Model/View/Controller partitioning.  Cocoa/Objective-C developers find MVC second nature, but that isn't so much an attribute of Objective-C.  I write MVC C++ code too.

Performance has not been a problem for me. We profile and in some cases replace Objective-C method calls with C functions or even in-line macros.  The vast majority of the application time is spent actually rendering 3D, and I spend a lot more time experimenting to find fast paths to milk more performance out of OpenGL than any other optimization.  The dynamic flexibility of Objective-C actually makes the process of bending over backwards for OpenGL much less painful.

I sometimes use PyObjC or FScript to enable rapid development and scripting.

----

If you want to flex your C# skills (instead of developing your own engine/etc.) in a 3D environment, try Unity (unity3d.com). Browse their forums for new challenges and contract work maybe...

