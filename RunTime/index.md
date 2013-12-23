---
layout: page
title: RunTime
---



The RunTime is an important part of ObjC. It is the underlying system that implements passing messages and determining object types while your application is running.  It is more or less invisibly linked to your application code.  

It is used internally for MultipleDispatch, and also allows you to query AnObject to ask if can perform a method.

The RunTime is implemented using C functions and used by the framework classes and classes you create. See InstanceMethod and ClassMethod.

The RunTime also lets you modify class hierarchies and method dispatch on the fly with techniques such as ClassPosing and MethodSwizzling.

You can use the open source software RuntimeBrowser [http://code.google.com/p/runtimebrowser/] to browse Objective-C runtime.

