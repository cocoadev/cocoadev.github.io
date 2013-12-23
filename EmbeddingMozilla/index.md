---
layout: page
title: EmbeddingMozilla
---

Embedding Mozilla in Mac OS X Cocoa Apps

http://www.artlogic.com/articles/Embedding_Mozilla/

J. Michael Caine, Senior Engineer, Desktop Systems, Art & Logic, 7/10/2003


Introduction

"Embedding Mozilla" can mean many things. For our purposes, it does not mean "embedding Gecko", the underlying engine. Furthermore, there are many "flavors" of Mozilla, one of which is named Chimera. Chimera is a standalone browser built in Cocoa, on top of shared Mozilla sources (which, in turn, are on top of Gecko), for Mac OS X. For our purposes, "embedding Mozilla" actually means "embedding Chimera", or, more accurately, "embedding CHBrowserView, which is based on Chimera".

Chimera is now called Camino.

....

Why does this person keep calling it "NSBrowserView"? It's CHBrowserView. It is misleading and confusing (and just factually incorrect- it's called CHBrowserView!) to call it NSBrowserView. NS is for official Apple Cocoa classes. -- AdamAtlas

Send the author a note to let him know.  -- Bruce

