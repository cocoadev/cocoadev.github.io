---
layout: page
title: FindingSourceLineFromCrashLog
---

Can I use information from my program's crash log:

    
Thread 0 Crashed:
0   libobjc.A.dylib                	0x908311ec objc_msgSend + 0xc
1   ...nyridgesoftware.validinator 	0x0000a888 -[AuvalOutputController dealloc] + 0x4c
2   com.apple.CoreFoundation       	0x9019440c __CFArrayReleaseValues + 0x214


to identify a line of source code? I know that something in A<nowiki/>uvalOutputController's dealloc method crashed while attempting to send a message, but that's as much as I know how to decipher. I'm using Xcode 1.5.

----

If you can reproduce the crash, simply run your executable in the debugger, then the debugger will stop at the erroneous line and show a stack dump, where you can see the context of each stack frame.

Though I can say with 90% certainty that the crash you are seeing is because you send a message to a deallocated object.

Either look over the code for dealloc or read NSZombieEnabled for some advice to catch this type of error.

--AllanOdgaard

----

Are you using a Development build or otherwise generating debugging symbols? If so, you should get a filename & line number at the end of the crash log line.

I think I'm generating debugging symbols. In the Build Style options "Generate Debug Symbols" is checked and "Level of Debug Symbols" is set to All Symbols. Are you saying that I should see the filename and line number at the end of each line of the above sample? If so, I wonder what other options I need to set.

----

How about the optimization level? Mine is set to None (-O0)

----

Now I'm getting line numbers. I didn't think I changed anything, but apparently I did. Maybe I inadvertently had the wrong build type set. Oh well...

A couple days after I posted the original question, Apple was kind enough to include a reference to a tech note about Crash Reporter in the ADC mailing. http://developer.apple.com/technotes/tn2004/tn2123.html. It doesn't talk about using this information in the context of Xcode, but there is some useful info there nonetheless.

Here's something I figured out: the     atos command-line utility described in the tech note requires Zerolink to be disabled. 

-SteveNicholson

----
Ummm...in my experience, the error you posted above usually means that the program crashed before it even got to your code (i.e., objc_msgSend was probably trying to send a message to one of your methods). When you get an error in your code you do get the line numbers. --JediKnil

The original stack trace **does** have his code in there - the second line. Looks like his crash is coming from a stripped executable, hence no line numbers.  His code is trying to do an objc_msgSend on something bad.  Were It My Code, I'd look at my -dealloc and see what I'm doing there.  Then use     atos to map the address back into the program (assuming you have the original program with debug symbols)

*Yeah, you're right, sorry about that. If I had to guess offhand, I would agree with you about the build settings. Perhaps the build mode was set to Deployment instead of Development...or something to that effect. I guess I'm not being too helpful. --JediKnil*

----

The best cocoadev page for stuff like this is DebuggingTechniques.      atos is described in a tech note linked from there, and there are many other techniques.

