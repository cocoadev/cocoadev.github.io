---
layout: page
title: HowToGetSerialNumberOfCD
---

Hi all,

How to get the serial number of a CD on Mac OS X? Is there any 
replacement for the function GetvolumeInformation which gives the 
serial number on Windows. If not how I can get a unique number that 
identifies the CD.

Thanks.

----

I haven't used this in anger, but it might be a useful starting point: http://developer.apple.com/samplecode/AudioCDSample/index.html

Also, take a look at the header at /System/Library/Frameworks/IOKit.framework/Versions/A/Headers/storage/IOCDTypes.h which the above code references. It contains something about Media Catalogue Numbers which is perhaps what you are after.

hope this helps!

