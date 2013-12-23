---
layout: page
title: NSMakeRect
---

NSMakeRect() is a function that is used to create (can ya' guess?) an NSRect.

The docs say:

NSRect NSMakeRect(float x, float y, float w, float h)

The first two arguments are the origin and the second two are the size.

An NSRect is an NSPoint (origin) and an NSSize.

You can get values from an NSRect by using:

    
NSRect myRect = NSMakeRect(10,20,30,40);
myRect.origin.x; // Equals 10
myRect.origin.y; // Equals 20
myRect.size.width; // Equals 30
myRect.size.height; // Equals 40


A quick look at the Functions reference (always remember that...) and the headers will tell you this.

-SamGoldman (minor change by KritTer)

