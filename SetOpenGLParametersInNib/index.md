---
layout: page
title: SetOpenGLParametersInNib
---

I've written a model viewer for halo. It correctly loads the models, and applies the textures. However, there was a problem with the depth buffer - when you looked at the model from certain angles, the depth buffer would be messed up. It was a long time before I realized that you need to give the context a depth buffer in the nib. Okay, problem solved, right? Well, for some reason, whenever i turn on the depth buffer in the nib, the View only shows black. Nothing but black. It IS drawing the vertices, but it only shows black. Does anyone know why this might be?

BobInDaShadows

----

Did you specify a useful glDepthFunc?  I can't remember what the default is, off hand.

--JeffDisher

----

Yes, I have it on GL_LEQUAL. I tried a bunch of other ones, none worked.

----

I'm no Cocoa OpenGL expert, but I don't recall setting depth buffer parameters in the Nib file. All I did with IB was to drag a custom view to my window and then set it to my NSOpenGLView subclass.

In my code I manually create an NSOpenGLPixelFormat and I give it to the context. it's in that format that I specify things like stencil buffers and depth buffers and whatnot.

--ShamylZakariya

*You can change these attributes through IB only if you drag an OpenGL View into the window - not a Custom View.*

*I had no idea!*

