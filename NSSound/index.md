---
layout: page
title: NSSound
---

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Objc_classic/Classes/NSSound.html

NSSound is a simple interface for playing back sound files such as AIFF, WAV and .snd files.

According to documentation, NSSound only support 16-bit, 44.1kHz an 22.05kHz audio files, mono or stereo. However, in 10.4, it is possible to load a wide variety of sound formats (as supported by QuickTime).

For more demanding applications QTKit might be a better choice.

NSSound spawns a thread to play the sound. When it completes, the delegate is notified in the secondary thread via sound:didFinishPlaying:. 

Note that you need to rename aiff files with the .aiff extension, NSSound will not find .aif files.

Getting a list of system sound files to play with +soundNamed is a real pain. I've added a +availableSounds class method to NSSound to make getting as list of available system sounds much much easier. You can download it here: http://mabblog.com/getfile.php?file=59 -MichaelBianco

