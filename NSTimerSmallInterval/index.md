---
layout: page
title: NSTimerSmallInterval
---

Hi,

I have been trying to make some applications using a lot of the NSTimer class. Can someone please tell me if NSTimer can support really small time Intervals accurately. I have a function that has to get called 48000 times every second. This is for making an on and off ramp for the sine wave that I am playing. 

Any suggestions are appreciated.

Thanks a lot,
 
Shaun.



*Well, you could just have a for(;;) loop :).*

**That uses up a ton of memory, though. Why exactly do you need a 48 KHz function anyway? Neither the human eye nor the human ear can refresh nearly that fast. Neither can the monitor, for that matter. Anyway, according to the docs, "the effective resolution of the time interval for an NSTimer is limited to on the order of 50-100 milliseconds." (See AnimationTimingAndCocoaDiscussion). Is there anyway you can skip about a thousand "frames" at once? --JediKnil** 

----

The reason I need a 48 KHz tone is because of the following. 

I will be playing a tone that will be up to 20 KHz. According to the Nyquist theorem, while getting the wave structure from an analog wave(or the opposite in my case), the frequency with which the amplitude has to be determined should be twice the actual rate which in my case would be 40 KHz. So practically anything over 40 KHz is ok. I'm thinking of 48KHz since I'm using an existing function that ramps up at a 48 KHz rate.

Also, could you please tell me how I could do it with a for loop(it would be a start). I'm still a newbie with Cocoa and its taken me a lot of time just to have control over the channel outputs and the sine wave. 

Thanks.

Shaun.


----

Cocoa is mainly an Application Programming Interface (API). NSTimer is really for animation not for discrete waveform generation. You will be better off working with Core Audio (see MTCoreAudio) for audio IO. MTCoreAudio is an Objective C Framework, so once you get your feet wet, you will be able to work Core Audio into a Cocoa App. 

--zootbobbalu

----

I actually am using Core Audio to generate the Sine Wave. I'm also using MTCoreAudio for the volume control(not amplitude, the volume control is for pan) since its far more easier than using Core Audio.

Can you please tell me exactly how I could use Core Audio for the above task? I would really appreciate any input here.

Shaun.

