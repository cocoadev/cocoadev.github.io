---
layout: page
title: DotMacSyncingLikePanicsTransmitFavourites
---

I searched the web but only found sites about how to put data onto the idisk, but that's not what I'm interested in. I'm interested in syncing stuff like Panic does with Transmit's Favourites (click on the .Mac panel in the System Preferences and there'll be a Transmit Favourite entry if you have it installed... at least on leopard there is).
Thanks.

----

Either you use the DotMac.framework (see the .Mac SDK at http://connect.apple.com), or (more likely the one you want) you use Sync Services, register your client and off you go: http://developer.apple.com/documentation/Cocoa/Conceptual/SyncServices/SyncServices.html#//apple_ref/doc/uid/TP40001149.
----
Is there a tutorial around somewhere, maybe?
----
There's plenty of sample code in /Developer/Examples.  .Mac exposes itself as a server service. --K

