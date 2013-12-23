---
layout: page
title: ImagesWithinFramework
---



I've made a framework, but the problem is how do i access the resources in that framework.

if i use [NSImage imageWithName:@"someimg"]; it looks for the image in the current application.

----

Use NSBundle to get a path to the image file, then use NSImage's initWithContentsOfFile:.

*To get the NSBundle, use     +bundleForClass.*

