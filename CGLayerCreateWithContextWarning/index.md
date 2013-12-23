---
layout: page
title: CGLayerCreateWithContextWarning
---



Hi all, I have the following:

    

CGContextRef *mainGraphicsContext = [[NSGraphicsContext currentContext] graphicsPort];

	//Set up a cglayer
	CGLayerRef *theLayer; //The actual layer
	CGContextRef *theLayerContext; //The context of the layer
	CGRect layerRect = CGRectMake (0, 0, 400, 400);
	
	theLayer = CGLayerCreateWithContext(mainGraphicsContext, layerRect.size, NULL);



But I get a warning "passing argument 1 of 'CGLayerCreateWithContext' from incompatible pointer type."

As far as I can see - this should be expecting a CGContextRef - just as I'm giving it ?!

Any ideas?

----

Try this:
    CGContextRef *mainGraphicsContext = [[NSGraphicsContext currentContext] graphicsPort];

//Set up a cglayer
CGLayerRef theLayer; //The actual layer
CGContextRef *theLayerContext; //The context of the layer
CGRect layerRect = CGRectMake (0, 0, 400, 400);

theLayer = CGLayerCreateWithContext(*mainGraphicsContext, layerRect.size, NULL);

Make sure to watch your pointers! CGLayerCreateWithContext doesn't return a pointer, while CGLayerCreateWithContext's first argument shouldn't be a pointer either.

----


D'Oh - thanks for your response... just seen the pointer problem. Arg!

Thanks again!

----

The code above will likely crash. As a rule, you should use the Ref types like simple types (i.e. int or long).
Currently the code initializes a pointer with a graphicsPort, then dereferences the graphicsPort, while it should pass the pointer.

Probably better code (not tested):
    CGContextRef mainGraphicsContext = [[NSGraphicsContext currentContext] graphicsPort];

//Set up a cglayer
CGLayerRef theLayer; //The actual layer
CGContextRef theLayerContext; //The context of the layer
CGRect layerRect = CGRectMake (0, 0, 400, 400);

theLayer = CGLayerCreateWithContext(mainGraphicsContext, layerRect.size, NULL);

----

Just to make it very clear, in case anybody is still missing the point,     Ref in a type name is equivalent to *. So when you see     CGLayerRef you can mentally translate that to     CGLayer *. Cocoa tends to use the direct pointer notation, but other Mac OS X APIs prefer     Ref; keeping this in mind will probably save you some pain.

