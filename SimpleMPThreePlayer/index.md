---
layout: page
title: SimpleMPThreePlayer
---

I want my app to play MP3 files, from a path to the music file. I know there's something called MPG123 that can be annexed to my project, but of course, it didn't work. I downloaded a patch to make an executable from the source files, but in the terminal entry building, I got an error.

Anyway, I still don't understand why the source comes in some 70 different files. I really don't know which file is important, neither which one can make my app play a MP3 file...

Can anyone clarify the situation ? Thanks...

----

Personally, I'd just use QuickTime to play the MP3 file. Just get a movie reference to the file and play it. If you don't want to use NSMovieView, the underlying QT calls are very simple.

-- MikeTrent

----

Where can I find reference doumentation on QT calls ?
Actually, I just want it to be as simple as possible.
Play, pause, resume, and stop.
No need for a paths list, only one path will be needed at a time... -- Trax

----

Most of the references and documentation you will ever need can be found by starting here: http://developer.apple.com

For QuickTime in particular, look here: http://developer.apple.com/techpubs/quicktime/quicktime.html

If you can't stand reading the docs, this page has a Cocoa project that takes an mp3 file and plays it in a window: http://www.scifihifi.com/weblog/mac/NSMovieViewAndMP3s.html

-- JoeZobkiw

----
Also, MacEdition has thing on simple sound programming at http://macedition.com/bolts/bolts_20030509.php , including playing an mp3 with quicktime.

++MarkDalrymple

----

Good. That last address helped me a lot. But there's something I can't avoid. After about 10-15 seconds of playing, the tune stops, but the slider continues on its way. In other words, everything is still playing, except I don't hear the tune. Is it something related to memory management or maybe I have to implement some kind of buffer ? Any clarification would be appreciated... -- Trax

----

I get that with iTunes more or less randomly. Never sure which track it's going to affect, but it always does it after that sort of delay. Another symptom is that it continues playing, eventually. Does anyone know if there's a filed bug on this? -- RobRix

----

Did you try playing the same MP3 in iTunes, or maybe on another computer?  I have seen MP3s that seem to be made this way (probably by the record company) to combat music piracy. 
--DerekCramer

----

Yes, I have been listening to this tune for years, and I never found any problem. I tried with few other MP3s, always the same result. The tune stops being heard after a few seconds... -- Trax

----

If it works in iTunes and not with Quicktime (try playing it with quicktime player and see if it chokes party-way in), it's probably a bug in quicktime or something weird with your mp3.  iTunes doesn't use QT for its playback, so it's possible that an implementaiton detail there will play the mp3, while QT might not.  In any event, bug it to apple, and maybe provide the mp3 so they can experience the problem first-hand.  ++MarkDalrymple

