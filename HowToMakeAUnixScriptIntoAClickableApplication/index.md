---
layout: page
title: HowToMakeAUnixScriptIntoAClickableApplication
---

Anyone know how to make a unix script into a clickable application?

----

**I seem to recall something about suffixing the script's filename with .command being enough for the Finder. Worth a shot?**

A .command file is a special document type for Terminal.app, which every MacOSX user will probably have installed. So if running in Terminal is okay, that's the simplest solution.

I've used this trick before with sh archives (self-unpacking shell scripts, to install a complicated component onto a customer's system) and it works quite well. --DrewThaler

----

Another way might be an AppleScript ... send your unix script through the     do shell script command from System Additions. Might need some extra tweaking to get it right, but it should be possible as long as no user interaction is needed.

----

If you want to make actual applications from shell scripts, Dropscript [http://www.mit.edu/people/wsanchez/software/] and Platypus [http://www.raunvis.hi.is/~ssv/software.html] are the two apps that spring to mind.  Also, source code is available for both so you can figure out how to do it yourself if you have bigger plans. -- Bo

----

Just chmod the script so that it is executable, and give it a .app extension (or set the file type to APPL). One thing to keep in mind is that such a script is double-clicked in the Finder, the working directory will be the root of the startup disk (i believe). An even nicer way is to create an application bundle around the script.

----
Pashua [http://q41.de/downloads/pashua_en/] and iHook [http://rsug.itd.umich.edu/software/ihook/] are other app of the Dropscript/Platypus ilk, with more emphasis on creating GUIs for user input/feedback.

---- 
If anyone still has the source code to dropscript, could they post it?  It no longer appears to be in the darwin repository.

