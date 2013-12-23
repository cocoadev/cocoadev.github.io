---
layout: page
title: XCodeProjectMoved
---

I moved (well, copied actually) an General/XCode project from one machine to another (using rsync -avE). But when I fire it up in General/XCode on the target machine, General/XCode says it cannot find any of the source files. I get errors such as "Could not find file /developer/src/M<nowiki/>yProject/Foo.h.  Perhaps it was moved or deleted?"  I can vi that exact path, so I know it's good.

Any ideas as to why General/XCode cannot see the source files?

----
It's probably not this, but it's worth checking to see if the files are referenced "relative to the project" or "relative to the build products" or whatever, rather than "absolute file path". --General/JediKnil

----
You can find it in the inspector of the red (missing) file entries, which you can bring up with the "i" toolbar button or command + i.
----
The path is the same on both source and target machines.  I did notice that the target machine is running a previous version of General/XCode which I suspect is the problem.  I'm going to upgrade to 2.4.1 and see if that helps.

----
Upgrading to 2.4.1 did not resolve this.  I checked the path type and it is Relative to Enclosing Group. Please help.

----
If you have a path that starts with "/", it *should* be set as an absolute path.

----
Yeah setting them to absolute path doesn't work either.  :(

----
Ok this is really strange.  The examples in /Developer/Examples have the same problem. Any ideas?

----
Well, I removed the Xcode.plist, restarted and everything is fine.

----
this just happened to me, and i found this page while looking for a solution.  my problem was a little weirder, because it didn't start with me moving or copying the project anywhere.  in my case, nothing of that nature had been changed, it was just that suddenly, for seemingly no reason, editing didn't work.  i didn't want to destroy all my xcode prefs if i could help it however ... but the suggestion to trash them made me recall that i had recently tried to experiment with using macvim as an external editor for source code files; should work, right?  well, it does pretty much ... but i didn't really use it, and subsequently pretty much forgot about it.  i can't be sure, but it seems that even after that change, the normal xcode editing was working.  in any case, when i went back to my project this afternoon, it wasn't, with the error described above.  perusing the xcode.plist with plist editor, i came upon this macvim entry (in General/PBXFileTypesToDocumentTypes).  out of more voodoo paranoia than anything else, i deleted it and saved the plist.  imagine my surprise when, upon restarting xcode, things seemed back to normal.  so if you don't want to have to trash all your xcode prefs, you might look into that ... 

----
Are you moving from a case insensitive file-system to a case-sensitive one? My first thought would be to use the 'Clean All Targets' from the build menu. (Sometimes General/XCode can hang on to invalid paths).

----
Removing Vim as the external editor for Xcode .c files fixed the problem for me too.     General/XCode -> Preferences -> File Types -> File -> Text -> Sourcecode

----
Just had this error message tonight, after a system crash while coming out of sleep. Nothing had changed, but Xcode 3.2.5 was running when the machine was put to sleep. General/MacVim is set to the external editor. Went into Preferences, and it complained that it couldn't find the General/MacVim editor (though General/MacVim was working fine, and hadn't moved). Reset it to use General/MacVim, and all was well.
