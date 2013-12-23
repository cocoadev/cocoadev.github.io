---
layout: page
title: BrowserBindingsSelection
---

Hey,

I have a Bindings/CoreData app that stores hierarchical data.  I have a tree controller which controls the data, and I bind my outline view to that controller.  Works fine, data is displayed properly, etc.  When I change selection in the outline, it properly updates the selection in the controller. (It's single selection only).  Now, I create a browser, and bind the browser to the tree controller.  Data displays fine, but when I select an item in the browser, it doesn't update the selection in the controller!

Here is exactly what I have set for the browser bindings:

Browser Content

    
     content
         Bind To: Item Controller
         Controller Key: arrangedObjects

     contentValues
         Bind To: Item Controller
         Controller Key: arrangedObjects
         Model Key Path: name



Thanks for any suggestions.

