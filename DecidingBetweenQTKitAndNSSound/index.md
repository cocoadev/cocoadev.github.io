---
layout: page
title: DecidingBetweenQTKitAndNSSound
---



Hello, owner of the skiTunes project (http://skitunes.iindigo3d.com/) here.

I recently started skiTunes from scratch, and I'm having difficulty deciding on what to use to play audio. In the old skiTunes we used NSMovie, and although it worked it was subject to skipping when the system goes under a load, as well as increased processor activity. Looking for alternatives I found NSSound and the newly created QTKit.

I need something that will play audio with low processor usage and won't skip. It also would help greatly if it would allow me to  add code for playing non-QT formats such as WMA and OGG with it, and it needs to be Obj-C, as I don't know C++. I'll also need to access its current playing time, position, and it should be able to pause/play correctly.

What do you guys think I should use? Thanks!
JohnWells

----

I've been using QTKit for a new project I'm working on, but found that it makes programming for Panther users difficult. I think it might work, but I'm not sure as I've moved to Tiger personally, and have no way to test apps on Panther. I'd recommend NSSound if it works well on Panther, since the requirements wouldn't be as heavy as QuickTime 7.

-- JasonTerhorst

----

A few weeks ago, I found a cool sound playing class called UKSound (although I don't really remember where I found it).  It uses frame buffers to make sure that the sound doesn't skip and what not.  http://www.levinvideo.com/jalstuff/UKSound.zip

