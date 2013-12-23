---
layout: page
title: CocoaJavaConstructorConfusion
---

I'm new to Cocoa.  I'm pretty decent with Java.  I've made a new project in XCode and I've setup my interface using Interface Builder.  I subclassed java.lang.Object in interface builder and linked all the controls in the interface to it.  My only problem is that I can't seem to be able to give my class a constructor.  When interface builder wrote the class file it didn't include one.  I put one in b/c I have code that needs to run when the application starts.  Is there another way that I should be doing this?  Thanks in advance.

----

I think you'll be wanting to use awakeFromNib. -- FinlayDobbie

