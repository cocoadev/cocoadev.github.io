---
layout: page
title: InterfaceBuilderCustomView
---



I've created a custom view together with an IBPalette and an Inspector.  I've successfully loaded the IBPalette into InterfaceBuilder & it correctly displays the view in the Palette window.  However when I insert the view into a nib file, by dragging it from the Palette window into an Interface, it doesn't get properly rendered.  When I test the interface it does.  

How do I get the custom view to render properly when I'm designing a new interface with it?

I know this may seem minor to people, but for some reason it's really irritating me!

----

This can be caused by not encoding/decoding all of the view's attributes properly.  Make sure that every variable/setting you use during the drawing routine can be initialised by the initWithCoder method.

