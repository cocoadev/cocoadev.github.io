---
layout: page
title: ElegantCoreDataSmartPlaylists
---

I am looking for an elegant way of making smart playlists (of songs) using core data. I'm not sure it's possible, but I was hoping for some brilliant solution :) I know that I can handle "normal" playlists and "smart" playlists as separate cases of the same thing, and do the former with a to-many relationship with songs, and the latter with a binding of a smart playlist entity to the predicate of an array controller, but this seemed a bit "special casey" if you know what I mean. If I were building this with normal classes, I would create an abstract playlist, and then have a method to return songs from it. This would abstract the "source" of the songs away from the implementer, and allow them to be defined as one wishes. So the static playlist would return just the contents of an array, while the smart playlist would query the db, and make an array of matches against the predicate dynamically (or whatever, but you get the point.) 

I was hoping to do that with core data, and thought that fetched properties might give me the solution. But they seem pretty static. I thought that maybe on a static playlist entity I could have a to-many relationship with songs, and on a dynamic one it could be a fetched property with a variable predicate. However, it seems that the predicates are set at design time and that I can't fool with them at run-time... or can I? I then made both those entities inherit from a generic playlist (with common properties) entity and then use that generic one to list the playlists, so that it shows both kinds. 

But then again, I only started cocoa a few days ago so I'm probably trying to fit a square box into a round hole :) Any suggestions on alternative, preferably elegant methods for implementing this useful feature are very weclome :)

Thanks,

DanielPeebles

----

Search the Apple documentation for the Predicates Programming Guide. Fetch requests can be formed in a number of ways.

