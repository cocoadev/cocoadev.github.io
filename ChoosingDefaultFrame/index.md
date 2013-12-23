---
layout: page
title: ChoosingDefaultFrame
---



I've been working with custom NSViews for a few months now, and while there's not a whole lot I haven't been able to figure out on my own, one thing that's escaped me is the best practice for choosing a frame when creating new view objects via initWithFrame: in my subclass. The problem is, most of my NSView subclasses have a frame in which the origin or the size are determined later when the components are laid out. Some have a fixed size, or a size that is fixed on one side, which I may or may not change later on. I'm not using IB for these controls, everything is done in code. 

Anyhow, what do most people do with initializers for custom views? Should I pass them {0,0} for origin and size, if that will be changed later? Would it be a good idea to have a class method to return a properly sized frame, and use that when creating instances of the class? Or, would it be a better approach to write a custom initializer, that doesn't accept any layout information, except maybe a float for or NSPoint when part of the size or origin actually does matter?

