---
layout: page
title: CompilingAppleScriptWithCocoa
---

Has anyone been able to run AppleScript from within a standard Cocoa app in XCode?  The tutorial at Cocoa Dev Central seems no longer to apply.  I found the page on NSAppleScript and was able to get that working, but using this technique you have to write the source in quotes directly.  I want to be able to execute a compiled applescript.  Thanks all.

----

So, answering my own question:  Here are all the things I did (I think) to get it to work:

1) Make sure you "Add File..."  (I had just created my applescript with "New File" and dragged the resulting file in XCode to the AppleScript Build Phase.)  When you add file, I think you have to check the box that tells you to copy the file.  

2) Make sure you create a new Build Phase (apple script).  Then drag your applescript into that build phase.

3) Once you build your project, go into terminal into the Build directory of your app..in particular into the Contents/Resources folder.  You should now see your script there.  (I think it used to appear in a Scripts folder, but that no longer seems to be there).

4) You want code something like this somewhere in your project:

    
NSURL *url=[NSURL fileURLWithPath: [[[NSBundle mainBundle] resourcePath] 
        stringByAppendingPathComponent:@"yourscript.applescript"] ];
NSAppleScript* script=[[NSAppleScript alloc] initWithContentsOfURL:url error:nil];
[script executeAndReturnError:nil];


So I got this to work with the simple "beep 4" script, but then I tried an (I thought) equally simply "display dialog 'hi there'" script and it just hangs.  Does anyone know anything about this?

----

I am not sure the Applescript build phase is even needed with this method. 

What worries me a bit, though, is that I suppose the Applescript needs to be compiled as it is stored as text in the application bundle. When does it get compiled? The first time it is executed, I suppose.

I would find it cleaner to be able to include compiled Applescripts, optionally making them run only.

� OlivierScherler

*

There is nothing to stop you from putting a precompiled applescript into your project.  You just can't use an applescript build phase to compile it for you automatically.  You could even use a shell script phase and the osascript command line command to compile a script automatically.

*

---- 

I doubt the original poster's method.  Quoting from the Xcode 1.1 release notes,

"Currently Java and AppleScript files are not supported in native targets, and therefore targets containing these languages should not be upgraded to native. The target upgrading sheet has been updated to point this out."

That is, applescript build phase doesn't work right now in a native project.

---- 

I don't really know what the previous quote from XCode means, but it definitely works.  It seems though that i didn't need to do the applescript build phase part.

*The point of the applescript build phase is to compile your applescript.  The way you're doing it it's being compiled at runtime.*

