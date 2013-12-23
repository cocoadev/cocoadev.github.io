---
layout: page
title: NSAppleScript
---

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSAppleScript_Class/index.html
----

NSAppleScript is a great tool in inter-application communication.

It can execute raw AppleScript code, compiled AppleScript code and AppleEvents. It can then return anything that has been generated in the external code.

Some hints:

* NSAppleScript is not thread-safe.
* The easiest way to execute an AppleScript on a multithreaded application is to make sure it always runs on the main thread using NSObject performSelectorOnMainThread:withObject:waitUntilDone:
* When you are placing AppleScript scripts inside your application (via Project Builder) and they are not compiled, make sure you move them into the AppleScript build folder in "Edit Active Executable." To do this select Edit Active Executable "ProjectName" from the Project menu in PB. Select Build Phases in the list on the left, right click and select New Build Phase -> New AppleScript Build Phase. After this, drag your AppleScript scripts into this new folder.
* To call an AppleScript script in your bundle, get the bundle location and append     @"/Contents/Scripts/applescriptnamehere.scpt". This will give you your compiled AppleScript script.  Note: Doing this in 10.4 today yielded a path of     "/Contents/Resources/Scripts/your_script.scpt".
* It is highly desirable to have your AppleScript scripts pre-compiled. If you cannot do this (say you are adding information to the script on the fly) then you are getting a very large performance hit.
*  If you do build from strings and use file paths, remember that AppleScript uses HFS-style, colon delimited paths. These can be made with CFURLCopyFileSystemPath(). See also Standard Additions / Misc. Commands / Classes / POSIX file.  "Tell application" can take a full path.


Sample source code:

* HowToCalliTunes

* a simple example :
    
 NSAppleScript *playScript;
 playScript = NSAppleScript alloc] initWithSource:@"beep 3"];
 [playScript executeAndReturnError:nil];

* loading from the bundle:
    
 NSString *scriptPath = [[NSBundle mainBundle] pathForResource: @"your_script" ofType: @"scpt" inDirectory: @"Scripts"];
 NSAppleScript *theScript = [[NSAppleScript alloc] initWithContentsOfURL: [NSURL fileURLWithPath: scriptPath] error: nil];

*[[KFAppleScriptHandlerAdditions



----

The AppleScript Language Guide:

http://developer.apple.com/documentation/AppleScript/Conceptual/AppleScriptLangGuide/index.html

----

Note that NSAppleScript is pretty much superseded by ScriptingBridge in Mac OS X 10.5 Leopard. ScriptingBridge is much easier to use, however it is only available to Leopard apps.

----

Not entirely true: NSAppleScript is still recommended for executing user-supplied scripts within your application (what AppleScripters call 'attachability), c.f. System Events' folder actions, Mail's 'run AppleScript' rule action, etc.

Users wanting to send Apple events directly from ObjC might also consider ObjCAppscript, which provides significantly better application compatibility than ScriptingBridge and works on Mac OS X 10.3.9 and 10.4.x as well. -- hhas

