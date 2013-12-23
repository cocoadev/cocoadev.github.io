---
layout: page
title: WaveformView
---



I am interested in creating a simple view to draw the waveform of the song that is selected/currently being played in iTunes. I am planning on using the General/EyeTunes framework for the interaction between my app and iTunes, but I was wondering if anyone would have any pointers on where to start to draw a waveform. I would rather not load the file and analyze it myself. Anyone have any experience with General/ArkonnektSound's waveform view? Is it stable?

----

Hello, I wrote Arkonnekt. The wave display is stable, but inflexible. The entire wave must be stored in memory. I would recommend against it if you want to display songs which may be several minutes long. I have been really busy recently, but am working on a refactored wave view. I have some stable code I could send you. It's BSD licensed. It is base C code for creating a peak cache and drawing a wave form only. You would have to tie it into Cocoa's General/NSView architecture yourself, I am taking a comprehensive approach to that and don't expect to be finished for a month or five. Good luck. -General/JeremyJurksztowicz

----

Jeremy, please use the reply tag so people know when one person's comments end and another's begin. It shows you to be a good guest with good housekeeping skills.

----

How does it draw the waveform? (ie Does it return a bezier curve of it, or just a value representing the given amplitude at a given point which would then be used to actually draw it, or what?) I'd love to have a look at that code.

-General/TylerStromberg

----
Sorry, but I have terrible housekeeping skills! In order to draw a waveform you have to "compact" many samples into one vertical line. Essentially you find out how many samples are represented by one horizontal coordinate point, and "scrunch" them together to get the local min and max values. For example, loading a wave with 1000 samples and displaying it on a 100 pixel wide view would require each horizontal point to represent 10 samples. So you take the minimum sample value and the maximum sample value, normalized for your view, and then draw a vertical line from one to the other. This pair of min and max is often called a "peak". Many sample editors will make peak cache files to speed up loading and displaying waves. Actually, without peak caches, wave display and editing can be painfully slow, depending on your machine and the size of the wave you are loading.

Now in practice, it is much more complicated than that, you have to deal with sample fractions, reading waves, cache transforms, zooming and scaling, a whole lot. That is not even including the loop-dee-loop required for realtime editing the wave. I would love to share my code with you all, but am in the middle of getting a new apartment in Shanghai, and I just started a new job, so I don't have time to spare right now. That being said, I will put my code on my General/IPod, find a dirty web-cafe, and put it up on sourceforge ASAP. Meanwhile, a place to get started is the Audacity project (in C++). I don't really like the coding style or layout, but with enough patience you can learn a lot from it. I am sure other open source editors are ok too. I am really looking forward to making my wave view a fully functional cocoa component with an IB palette and all, but don't expect that to happen too soon... :>( I miss programming... -General/JeremyJurksztowicz
