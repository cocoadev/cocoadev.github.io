---
layout: page
title: NSMutableStringProblemsOnPowerPC
---

Hi, I have been working on a program of mine for a few months now, and have finally gained access to a PowerPC computer to test it as a Universal Binary. The program works fine with no apparant problems on the Intel machines, but seems to error with any NSMutableString code on the PowerPC machines. I checked the crash log and it is telling me that it is rasing an error in the code: [NSMutableString initWithString:]. Has any one else encountered this problem and know how to resolve it? The code I'm dealing with converts an NSString to a NSMutableString.

    
NSMutableString *mutableString = [NSMutableString string];
[mutableString setString:s];
return mutableString;


Thanks in advance,
TomHancocks

----
That's a rather odd error...but are you using OS X 10.4 on the PowerPC computer, or 10.3? Some of the string bridging stuff changed with Tiger coming out. Also, you might have unrelated memory errors because you're creating an autoreleased NSMutableString. Finally, though, NSString already supports this with     -mutableCopy, which returns a mutable string with a retain count of 1. --JediKnil

----
Both computers are running 10.4. I'll try the mutableCopy and see what happens. Thanks

----
There might also be a problem with     s, you should check that out. (Internally,     setString: might just be calling     initWithString:)

