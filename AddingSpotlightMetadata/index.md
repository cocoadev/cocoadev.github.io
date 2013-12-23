---
layout: page
title: AddingSpotlightMetadata
---

Hi

I have been looking for a way to store arbitrary spotlight metadata to a file using Carbon, Cocoa or any other framework in OS X. The only thing I've found is spotlight importers (which is not what I want/need since I'm acting on files already indexed by the default importers) and AppleScript for setting the comments-field. 

It is easy to read metadata using MDItemCreate but is there any way (easy or not) for _writing_ metadata to a file?

/Gabbe 

----

I second this... the last time I looked I had the exact conclusion... You could set the comment field (limited to about 100 characters or so) but there wasn't any obvious way to attach arbitrary data that spotlight would pick up

Any pointers would be great

----

