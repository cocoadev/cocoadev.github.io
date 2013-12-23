---
layout: page
title: BindingsVersusActionsAndDelegates
---

I've wondered about possible conflicts between CocoaBindings and controls' action methods and delegates.

For example, if you bind an NSTableView's column values, and also set the NSTableView's data source, which does the table use, the binding or the data source?

So far, I notice that a text field's action gets called *after* its "value" binding's setter.

�DustinVoss

----

I have not drilled down on NSTableView's documentation for this specific case yet. In one of my projects I am currently populating a NSTableView via bindings but also have the     dataSource outlet set for Drag & Drop purposes. While I only respond to the Drag & Drop specific messages, it works just fine.

