---
layout: page
title: AppleScript
---




AppleScript is a technology AppleComputer employs for allowing software to be controlled via scripts. The Carbon API for is OpenScripting, the Cocoa API is NSAppleScript.

CocoaScripting wraps and dispatches AppleScript commands to the appropriate places in your application, and collects the results and sends them back to the AppleScript engine.

Apple provides some starting points at http://developer.apple.com/referencelibrary/GettingStarted/GS_AppleScript/index.html.

In Mac OS X 10.2 you can use the NSAppleScript class to compile and execute scripts.
One can also make AppleEvents programmatically in Cocoa via NSAppleEventDescriptor,
which can then be sent to an NSAppleScript  object or other apps.
See Apple's sample code at http://developer.apple.com/samplecode/AttachAScript/index.html
for example.


The rest of the page is mainly on implementing scriptablity in a Cocoa app.
----
The AppleScript Language Guide:

http://developer.apple.com/documentation/AppleScript/Conceptual/AppleScriptLangGuide/

----

Check out the developer docs at http://developer.apple.com/documentation/Cocoa/Conceptual/Scriptability/index.html
or you can find them as part of your local developer documentation:

[Jaguar] file://Developer/Documentation/Cocoa/TasksAndConcepts/ProgrammingTopics/Scriptability/
[Panther] file:///Developer/Documentation/Cocoa/Conceptual/Scriptability/index.html
[Xcode] file:///Developer/ADC%20Reference%20Library/documentation/Cocoa/Conceptual/Scriptability/index.html

----

Among other resources, here's an old Applescript-in-Cocoa article in MacTech:

http://www.mactech.com/articles/mactech/Vol.16/16.07/AppleScripttoCocoa/

----

Here's a recent article at the O'Reilly Mac Devcenter:

http://www.oreillynet.com/pub/a/mac/2002/02/22/applescript.html
Integrating AppleScript and Cocoa

----

or another one here (KFAppleScriptHandlerAdditions):
http://homepage.mac.com/kenferry/software.html#KFAppleScript

----

Check out AppleScriptingCocoaApp for a brief guide to implementing AppleScript support in your app.

Also check out HowToSupportAppleScript for a more extensive guide.

----

AppleScript is leaky... NSAppleScriptMemoryLeak

----

"The Development of AppleScript" (W. Cook, 2006) [http://www.cs.utexas.edu/users/wcook/Drafts/2006/ashopl.pdf] is a recent paper by one of the original AppleScript designers that provides some excellent insights into the early history and original objectives and design of AppleScript and the OpenScriptingArchitecture.

