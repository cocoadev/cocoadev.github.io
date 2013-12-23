---
layout: page
title: ReadingEnumerationsFromHeaders
---

I need to be able to read out enum declarations (identifiers AND values) from bundles that I am importing into an application as plugins. Header files in the plugin bundle and precompiled headers will be available in the usual /Headers directory. 

I see 3 options to explore:

*Do I have to do something with the header source code itself? //safest, will probably work but slow and have to scan for them
*Better, can I use a precompiled header (.p file) ? // not sure how to read them
*Can they be read from the object code itself? // I doubt it.


Maybe this should be on a C wiki but as we all know ObjC programmers are an extension of C programmers.

Any ideas? In particular does anyone know how to or have a tool to read a precompiled header?

-- MikeAmy

----

I know this isn't an answer, but I'm kind of curious: what sort of plugin design led you to want to do this?

