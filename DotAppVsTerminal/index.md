---
layout: page
title: DotAppVsTerminal
---



hi. so, the problem. i have an Cocoa/Carbon/C++ application, which is normal with running from the terminal - application is starting. but when i'm trying to start it by clicking on .app icon - it crashes with "may be damaged or incomplete"... what's wrong?

----

That means that it is missing something from the package structure.  Try creating a basic *Hello World* app in XCode (the default Cocoa application template should be good enough) and then take a look at what is in that .app bundle to get an idea of what you are missing.

--JeffDisher

----
nothing new. was builded, was clicked - nothing. was runned from terminal - it's ok. i'v scaned .app directory for getting structure - it's ok - like in my old application (i meen on the contrary). what's wrong i don't know...

----
Your Info.plist is broken.

Also, is it that hard to type capital letters, proper punctuation, and attempt to spell decently?

----
The best way to check Info.plist consistency is to open it with Property List Editor app, that comes with Xcode package. If there are any errors (typo's) in it you will the an alert - fix them in any text editor.

-- DenisGryzlov

----
This is good but not sufficient. It won't catch, for example, a mismatch between the Info.plist's CFBundleExecutable entry and the actual executable's name.

----
Sorry my english, i'm not speeking well yet )) I checked Info.plist - all is ok, CFBundleInfoExecutable is ok... No errors so far... 
    
CFBundleDevelopmentRegion    String    English
CFBundleExecutable                 String    mybinary
CFBundleIconFile                     String   
CFBundleIdentifier                   String    home
CFBundleInfoDictionaryVersion  String    6.0
CFBundleName                         String    mybinary
CFBundlePackageType              String    APPL
CFBundleSignature                   String    ????
CFBundleVersion                      String    1
NSMainNibFile                          String    MainMenu
NSPrincipalClass                       String    NSApplication

And what's strange is that HelloWord not runnig too! I meen by clicking. On the another system. The main word is ANOTHER SYSTEM. On the home it's ok
----
ZeroLink? (i.e. are you building a Debug/Development version or a Release/Deployment version?)

----
Release (zerolink disabled). Also i'm building with makefile (meen just compiling and linking) - the same effect

