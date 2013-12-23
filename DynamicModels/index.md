---
layout: page
title: DynamicModels
---



I'm developing a document based application using CoreData, but I have some questions about how to best model my data.  The issue I'm having is that I need to support plugins that should be able to also store data in the document's object context.  In essence, plugins loaded at runtime (only once at the start of the program) need to be able to extend the managed object model of my document.  However, I get the sense that modifying object models is not the best way to go, and although it may be permissible before the model is used, I don't know what kind of problems I will run into if the user adds or removes plugins, and then attempts to open an older document.

One solution that I saw being presented in a related discussion on the cocoa-dev mailing list, was to design a model whose data was essentially just the real model.  The approach was to simply use to-many relationships to some "NameValuePair" entity that essentially acted like an array of attributes that other plugins could add to.  However, this approach significantly limits the capabilities of CoreData, in that you will no longer get as much for free.  For instance, when designing the interface for, how would one bind a text field to one of the NameValuePair's?  It seems that using this approach would require a lot of custom coding, which sort of defeats the purpose of using Core Data to begin with.

Any ideas on how one should model plugin-based apps?

----

This is the reason I'm not using CoreData and using QuickLite instead. QuickLite is a database backend that allows adding tables and columns. I see no good way of CoreData doing what I want. QuickLite is a true database backend, which CoreData is not.

----

Well, I'm not really looking for a powerful database backend, just something that integrates nicely with IB and gives me things like undo support for free.  I wanted to make writing plugins for the application easy for other developers, and working with something like Core Data is (for the most part) much easier than having to implement all this stuff yourself.

