---
layout: page
title: CoreDataAsSaaS
---

I've become so accustomed to Google Doc et al's real-time collaboration that with every passing day I feel greater disgust for applications which keep file data on my disk with no sharing, let alone collaboration, built in.  So, I've been trying to find out if there might be an "easy" way to implement at least a minimum of collaborative functionality, ie can I save a document and have all other people editing that doc receive my changes.

To that end, I've been looking at NSAtomicStore, the designated subclassable subclass of NSPersistentStore.  However, I note that NSPersistentStore has an initWithURL: type method.  My question is, has anybody tried using this pointing to a URL on a webserver (as opposed to a filepath url)?  If not, does anyone suspect that such a ploy might work?  If so or not, would anyone care to recommend a suitable entry point for building collaborative core data apps?  It seems to me this should be easier than collaborative text-editing.  Thanks to whoever can point me in the right direction...

