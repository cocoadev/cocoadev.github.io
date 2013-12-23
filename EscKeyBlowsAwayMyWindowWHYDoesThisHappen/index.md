---
layout: page
title: EscKeyBlowsAwayMyWindowWHYDoesThisHappen
---


In my multi-document application when I press the Esc key at any time, my window disappears--data and all.  Gone forever.

Why does this happen?

How do I fix it?

Thanks everyone,

KentSignorini

----

I believe this means that you are using an NSPanel rather than an NSWindow for your window's class; switch it back to NSWindow and that behavior should disappear. -- Bo

----

Thanks, Bo.  That was it.

KentSignorini

