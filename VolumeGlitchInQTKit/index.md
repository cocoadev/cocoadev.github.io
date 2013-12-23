---
layout: page
title: VolumeGlitchInQTKit
---



I'm using General/QTKit in my media player program, and while I'm thankful General/QTKit is much better with CPU use, it seems to have an odd glitch: The volume is unspeakably loud when I try to control it via a slider. The slider is set to 100.0 and here's the code I'm using:

    
int volume = [sender floatValue]/1.0;
[video setVolume:volume];
[volumeSlider setIntValue:[sender intValue]];


Any idea what's wrong? I really don't want my program to blast people's music so loud it leaves them in the corner.

----

Dividing by one doesn't seem to make much sense.  Is the volume a value from 0 to 1.0 rather than 0 to 100.0?

----

This should work but make sure that the range for the slider in IB is set from 0 to 1.0

    
float volume = [sender floatValue];//The sender should send a value from 0 to 1.0,
[video setVolume:volume];
[volumeSlider setFloatValue:[sender floatValue]];//Is your sender the slider, if yes then I don't see the point of this line

