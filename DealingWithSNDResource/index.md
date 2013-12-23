---
layout: page
title: DealingWithSNDResource
---

Is there a Cocoa API that can deal with sound data in the form of a snd resource?

----

Do you mean an actual resource, in the resource fork of some other file that isn't a supported QuickTime format?

----

The example I am working with is a file, which appears in the finder as a playable sound (not with the QuickTime-like player thingie, but with a button that allows you to play the sound without any control over starting and stopping.)

When I open the file with a resource editor, the snd resource appears within the file. However, according to at least one app I have used to open the file, or play it, the sound is from "non-sampled" data. Therefore most sound apps (like Audacity) will not open the file.

The only way I have found of making its data more accessible is to play the sound in the Finder or other app and use something like WireTap to divert the output from the audio hardware to a QuickTime file.

The reason this is an interesting question (at least to me) is that the resource itself is stored on disk as a file of less than 4KB, and yet it produces over a minute of noise. I (who am not sophisticated about audio technology) am wondering just how they DO that....

----

Whatever you're using to look at the size of the file (I assume     ls or     wc or something like that) only shows the size of the data fork of the file, but the sound is stored in the resource fork.

----

Check out the resources that Apple has on Carbon and Resource Forks. I think there are some calls specifically directed at getting snd resources as sounds.

