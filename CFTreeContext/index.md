---
layout: page
title: CFTreeContext
---

CFTreeContext is a basic structure used to refer back to the data stored in the tree structure.

What/Which tree?  Where/When would someone use a CFTreeContext?

A CFTreeContext holds the data for each node, as well as retain/release callbacks. It does not know about parent or child nodes. Those are stored in each CFTree instance (each CFTree instance is a node). See the "CFTree" page for some options on how to use CFTreeContext.

