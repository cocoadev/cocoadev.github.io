---
layout: page
title: CaptureToAudioCodec
---



There are lots of examples for capturing audio from a live input (ie microphone or line-in) to AIFF, but nowhere can I find documentation to capture audio using one of quicktime's audio codecs (such as AAC).  Several applications, such as QTBroadcaster, allow for this.  In theory it can't be that difficult: record into a buffer, convert the contents of the buffer chunk-by-chunk, into destination format, and write to a file...  Can anyone point me in the right direction?

EcumeDesJours


----

Take a look at Apple's QuickTime API documentation--particularly 

**SoundConverterOpen** for specifying the audio format and creating the SoundConverter object:
http://developer.apple.com/documentation/QuickTime/APIREF/SOURCESIII/soundconverteropen.htm


and **SoundConverterConvertBuffer** for actually performing the conversion on the buffer:
http://developer.apple.com/documentation/QuickTime/APIREF/SOURCESIII/soundconverterconvertbuffer.htm


And here are some constants for the formats that the SoundConverter API should be able to handle:
http://developer.apple.com/documentation/QuickTime/APIREF/SOURCESIV/soundformats.htm


Keep in mind the format of your audio input source (if you're recording from live input with CoreAudio, it'll probably be 24-bit PCM floats) when you create your SoundConverter object.
Finally, if you want to write this converted stream to disk, you'll probably want the file to be readable by some program other than your own. This will entail that you write an appropriate header for your sound file. There are functions to do this automatically--take a look at the source code for Apple's Daisy example.
The CoreAudio toolbox API will probably be extremely helpful as well:
http://developer.apple.com/documentation/MusicAudio/Reference/CoreAudio/audio_toolbox/chapter_4_section_4.html

And for writing an AIFF/AIFC header, you will probably find the following reference extremely useful:
http://developer.apple.com/documentation/QuickTime/APIREF/SOURCESIII/setupaiffheader.htm

