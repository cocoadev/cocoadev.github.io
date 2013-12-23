---
layout: page
title: AudioQueue
---




From Audio Queue Services Reference:

An audio queue is a software object you use for recording or playing audio in Mac OS X. An audio queue does the work of:

* Connecting to audio hardware
* Managing memory
* Employing codecs, as needed, for compressed audio formats
* Mediating playback or recording

Audio Queue Services enables you to record and play audio in linear PCM, in compressed formats (such as Apple Lossless and AAC), and in other formats for which users have installed codecs. Audio Queue Services also supports scheduled playback and synchronization of multiple audio queues and synchronization of audio with video.

Reference: http://developer.apple.com/documentation/MusicAudio/Reference/AudioQueueReference/Reference/reference.html

Playback Example: http://developer.apple.com/samplecode/AudioQueueTest/index.html

---- 
AudioQueue on the iPhone/iTouch:

AudioQueue allows lower-level access to iPhone/iTouch sound (e.g. sample-by-sample playback, sound synthesis).  

AudioQueue output by default uses the main speaker.  

AudioQueue input by default reportedly uses the line-in port.  To use the iPhone's internal microphone, routing must be performed.  How this is done directly is currently unknown.  A workaround for routing the microphone to the AudioQueue is to activate an AVRecorder once the AudioQueue is already running.

Calling AudioQueueDispose with a "false" parameter will leave an extra thread running in the mediaserverd  process, as opposed to calling it with "true".  It is unknown whether this is intended behavior, but it is advisable to use "true" to dispose of the AudioQueue immediately.

