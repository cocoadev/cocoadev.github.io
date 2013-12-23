---
layout: page
title: InfraredportonLombard
---

How do I access the IrDA port using Cocoa?  There are various discussions on building lists of i/o devices using Carbon routines, but is there a Cocoa API?

The IOKit is not Carbon. It is a C API, but it isn't Carbon. Look it up on Apple's site, or in the /Developer/Documetation or /Developer/Examples folders.

see here:

http://developer.apple.com/samplecode/Sample_Code/Devices_and_Hardware/Serial/SerialPortSample.htm

It's well written easy to read code (with lots of comments) - read it through a couple of times and add lots of NSLogs to find out how it works.  Also read the Apple documentation of all the different functions they use.

I think once you've got the fileDescriptor you can use a bit more Cocoa, especically NSFileHandle.

Search Cocoa.mamasam.com for NSFileHandle and serial and that should help too.

----

Or you could look at the AMSerial class here:
http://www.harmless.de/cocoa.html

