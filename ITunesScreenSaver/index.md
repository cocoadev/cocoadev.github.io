---
layout: page
title: ITunesScreenSaver
---


A few related questions:

-  Does anyone have any idea whether it would be possible to have a screensaver pull the frequency-spectrum/waveform data while iTunes is playing (like a visualizer does), if the saver happens to be invoked while iTunes is open?

- Would it be possible to make a framework that could be added to a screensaver source base to make it compile as a visualizer?

- Has anyone made a framework which could be added to a screensaver project (similar to SaverLab I'd imagine) that would make it compile as an app that would simply run the saver code, for debugging purposes (or "incremental source modification"), and then just removed when you want to compile the final saver bundle?

----

I would imagine you'd create a plug-in for iTunes just like any other visualizer plugin that 'gets' the data. Using a bit of interprocess communication, the screen saver can pull the data if it's available. Hell, using Rendezvous, you could conceivably pull visualizer data from somebody *else's* iTunes on the network (provided they've got the plugin and iTunes is running and the service is shared, etc ...).

----

Another possibility:  the screensaver could just be a stub to activate and deactivate the iTunes full screen visualizer, if that's what you're after ...

----

is it something like this you want? http://www.macupdate.com/info.php/id/17193
I made it with a bit of applescript in a general screensaver

