---
layout: page
title: SoundInScreenSaver
---

I want to play a sound in my screen saver, but it doesn't seem to work. I converted the sound to an aiff, 44.1 kHz, 16 bits, st�r�o... I coded like this :
    
[[NSSound soundNamed:@"paf"] play];


I tried with and without the extension, I tried to create the object, and then play it. No result. Can anyone help ?

-- Trax

----

Try adding the sound file as a Resource in your project then using something like this:

    
NSSound * mySound = [[[NSSound alloc] initWithContentsOfFile: [[NSBundle mainBundle] pathForResource: @"paf" ofType: @"wav"]] autorelease];
[mySound play];


Your original code may also work, as adding the sound file as a resource is probably the key.

--
Tantle
----

I think that NSSound will only locate sounds located in the main bundle (the System Preferences.app bundle, or the ScreenSaverEngine.app bundle, in the case of a screen saver module) (and so will the second suggestion in this thread).

You will probably have to do something like this: 

NSSound *mySound = [[[NSSound alloc] initWithContentsOfFile:[[NSBundle bundleWithIdentifier:@"com.yourcomp.yourscreesaver"] pathForResource:@"paf" ofType:@"wav"]] autorelease];
[mySound play];

-- DavidRemahl

----

Well, I don't understand anything. Why doesn't my line of code work ? Anyway, I tried your ideas, and it doesn't work. But I think I did not put my file at the right place. I just did "Add Files..." and placed the reference into the "Resources" folder. How do I add a file as a resource ?

-- Trax

----

This same question was asked in the ChatRoom, the solution was using something similar to DavidRemahl's approach (which would work too) only finding the plugin by class instead of identifier (using NSBundle's bundleForClass method). I assume it is the same person asking, and if he found it easier to do it this way so might someone else. Join the chat! we don't bite.

-- GormanChristian

