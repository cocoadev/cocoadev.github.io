---
layout: page
title: OpaqueType
---



CoreFoundation's "objects" are all instances of some OpaqueType or other. And that's the limit of my knowledge. Does it just use a pointer-abstracted structure? -- RobRix

Hardly matters, but I guess so. I doubt they're ObjC things, which only leaves structures. -- KritTer

I don't think they're objects; I only added the entry because it deals with FoundationKit's cousin CoreFoundation and therefore is likely to come into the life of some Cocoa developer somewhere (it already has, actually). But you're right, their implementation doesn't matter because they're Opaque with a capital O for a reason. Encapsulation and all that. -- RobRix

