---
layout: page
title: AnEvent
---



Events are occurrences that the system recognizes: things such as mouse clicks, key hits, and the like.

When AnEvent is received by AnApplication, the FirstResponder in the ResponderChain is given the chance to deal with it. If it doesn't know what to do with it, it passes it on up the ResponderChain. If nothing deals with it, it is ignored.

