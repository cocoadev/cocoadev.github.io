---
layout: page
title: CocoaSoundWithQuickTime
---

My application has two parts, data entry and sound playback. The data entry part is something I can take care of. However, the sound playback seems very confusing although the task at hand is very simple.

1) All I need to do is take a playlist or an individual song from any location in the computer and have the application play the sounds according to some control that I have on my application. 
2) These sounds have to be outputted as digital signals since they will be going to a firewire mixer device. 
3) The output has to be two channel as I need to send out two different sounds through the channels.
4) Control the decibel value of the sounds.

I checked out the documentation and found that the ways through which this can be done are using CoreAudio or QuickTime. The new QTKit framework also came up and I have decided that it is probably the most simplest to use of all three. 

However, like I said before I need help with this framework. Can anyone just show me a few snippets of code that can accomplish the above task? Anything would help. Or can anyone show me a better and easier way to do it.

Thanks a lot.

Aoro Shalia.

----

Open a sound file:

    
QTMovie *movie = [[QTMovie alloc] initWithFile:pathToFile error:NULL];


Play the loaded sound:

    
[movie play];


Set the volume:

    
[movie setVolume:volume];


Now, not to be rude, but... did you have any questions that can't be answered with a five-minute look through the docs? It usually doesn't pay off to ask such basic questions, you'll take less time just looking it up.

----

I understand the sound playing part, what I fail to understand is how to keep the ouput digital and two channel.

Aoro.

----

I don't really understand what you mean by "digital". All output from QuickTime is digital. Your computer is digital. Whether the sound gets converted to analog before it leaves your computer depends on what audio device you're using. If you're using an audio device that outputs digital, then you'll get digital. If you're plugging in headphones, obviously you'll get analog.

As far as the number of channels, you'll get two-channel output if your sound has two channels, just as you'd expect.

----

Does that mean that no matter how many channel sounds I have it will automatically be split up? Is there a way to control this or in general how do we control the channel ouput using the QTKit? Also how do I set the volume control using Core Audio? The reason is that although the setVolume method(of QTKit) can control the Volume, one can easily change it through the keyboard. How do we override this and access the master volume control for the system?  

Aoro.

----

This question keeps coming up, and unfortunately there is no simple answer. You need to use core-audio if you are going to interface with funky firewire drivers. This means you need to put away a bit of time and learn it. Sorry, apple has a great audio architecture, but it is totally inaccesible to newbies. Check out /Developer/Examples/CoreAudio for some code, in the PublicUtility folder there is a FilePlayer-NEW api, It can do all the streaming for you. There should be other examples there to show you how to access a device, including it's channel layout.
-Jeremy Jurksztowicz

