---
layout: page
title: ValueTransformerWithoutBindings
---

General/ValueTransformerWithoutBindings

I've been wondering if it's possible to set a value transformer up for my table view, but I'm doing some calculations to generate the data source's info so I can't (?) use the controller layer for the content.  Basically for one of the columns I would like to have the data source return an General/NSString with the path to an image file and have the value transformer take the string and return the image for the image cell formatter in the table...

table view -> data source (path name) -> transformer (image) -> image cell

Is this kind of arrangment possible?

----

sounds to me like you need to refactor...

table view -> view controller -> data controller (path name) -> view controller (image) -> image cell

MVC is too sparse in my opinion.  If you only have one controller layer, that controller layer is thick, and the methods are generally messy.  I think it should be MMDV (Model, Manager, Delegate, View) where view displays, delegate responds to views and users, manager manages data and model represents the data... Appkit already sorta-kinda does this with the document architecture.  The advantage of breaking the traditional controller layer into two parts is that your code is cleaner, your logic for how to handle your data is easily shared between different parts of your application and more... For most of my apps the model layer is foundation, view is appkit, and then anytime I need a message sent or recieved from a view it goes through a delegate.  Anything that plays with foundation asks a manager to do it.
