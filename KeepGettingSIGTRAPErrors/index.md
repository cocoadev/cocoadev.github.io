---
layout: page
title: KeepGettingSIGTRAPErrors
---



OK, I keep getting a SIGTRAP 5 error when I try and run my application using iLifeControls.framework. When I uncheck the framework, the problem goes away but obviously I can't access the framework in the application as it wasn't included in the build. How can I get this framework to work properly?

I have included a ZIP file of the project here: http://www.maxnaylor.co.uk/Conjugatrix.zip

----

    

dyld: Library not loaded: @executable_path/../Frameworks/iLifeControls.framework/Versions/A/iLifeControls
  Referenced from: /Users/diciu/Desktop/Conjugatrix/build/Debug/Conjugatrix.app/Contents/MacOS/Conjugatrix
  Reason: image not found

Conjugatrix has exited due to signal 5 (SIGTRAP).


Your build is missing the iLifeControls dynamic library. OS X expects to find it under the "Frameworks" directory of your application binary.


XCode can copy the framework bundle as part of the build process if you place the iLifeControls under your project and add a "Copy Files Phase" with the destination set to "Frameworks".
If you want a step by step procedure check out the Sparkle project - they have very good documentation on how to use external frameworks from your application.

--CristianDraghici

