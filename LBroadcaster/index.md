---
layout: page
title: LBroadcaster
---

LBroadcaster was a C++ mixin class in the PowerPlant application framework which  implemented the subject side of the Subject/Observer pattern.

The primary responsibility of an LBroadcaster was maintaining a set of LListeners that could receive messages and then sending the messages to those listeners when directed to do so.  A message, embodied by the MessageT data type, was a 32 bit integer message and was accompanied by a parameter which was of type (void *).

The LBroadcaster class included methods to add and remove listeners, as well as the ability to control whether or not the broadcaster was currently emitting messages.

