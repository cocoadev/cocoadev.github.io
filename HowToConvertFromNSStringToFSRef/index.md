---
layout: page
title: HowToConvertFromNSStringToFSRef
---



I am using a save panel to get the user's desired directory and filename. I would like to pass these two NSString values to a function that requires an FSRef for the directory name and a CFStringRef for the filename. What can I do to get these two strings in a format that is compatible with FSRef and CFStringRef? 

Thank you in advance for any help.
----
CFStringRef is easy, it is toll free bridged with NSString, which means that they can be used interchangeably
For the FSRef you could create an NSURL with the path. NSURL is toll free bridged with CFURLRef, so you can call CFURLGetFSRef on it. Alternatively get a UTF8 representation to the string and stick it into FSPathMakeRef

FrederickCheung

