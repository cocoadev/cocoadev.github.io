---
layout: page
title: CurrentDirectoryPathChanged
---

I have an app that writes several temporary files upon launch, into a folder called Temp that it assumes to be in the same directory it's in. It checks for this folder during the writing of these files, a la     [[[NSFileManager defaultManager] currentDirectoryPath] stringByAppendingString:@"/Temp"]. A new problem has suddenly appeared. Essentially, if I launch the app from Xcode, currentDirectoryPath returns the absolute path to the current directory. If I launch the app from the Finder, currentDirectoryPath is just returning /. This means that the app is checking for the Temp folder in the '/' directory, the top level of the HD.

Anyone else have this?

----

You should never depend on your current path's value in a Cocoa app. You should also never write files to a directory that's assumed to be at the same location as your app - what if the user doesn't have write permissions for that directory?

Create your temp files under ~/Library/Application Support/  or ~/Library/Caches/ or one of the system tmp directories.

----

Is there any difference between the uses of Application Support and Caches? That is to say, are certain types of files/files which serve a certain role supposed to go in one place or the other?

----

I would say that App Support should contain persistent data, such as templates or customized resources, and make sure you implement the ~/Library, /Library, /Network/Library inheritance to fully take advantage of that power.  ~/Library/Caches is for things that are transient, and can be deleted at any time to reclaim space, or clean up the drive for imaging, or something like that.  For example, AB uses ~/Library/Caches to create a per-file representation of your address book so that spotlight can index it.  This information can be deleted at any time, AB.framework will just recreate it later.

----
not answering that question, just adding my 2 cents:

Cocoa has some convenient methods for path manipulations. In your example, that would be

    - (NSString *)stringByAppendingPathComponent:(NSString *)aString

----

Come to think of it, how do you pass in ~/Library/Application Support as a path? In UNIX, you'd put quotes around the last part, to get ~/Library/'Application Support". That'll get you there in the Terminal. I tried putting this into an NSString, then calling NSString's stringByStandardizingPath to fully expand it. This prints out what seems like a completely valid absolute path. If I try to give that path to NSFileManager though, it's not recognizing it. I just get falses for NSFileManager's fileExistsAtPath: isDirectory: and createFileAtPath: contents: attributes:

----

Actually, quoting spaces and other characters in pathnames has nothing to do with unix.  The reason you have to do it in Terminal is because of the shell, which needs to figure out where the path/command ends and the arguments begin.  ~ is also a shell specific thing, which is why you have to call stringByStandardizingPath or stringByExpandingTildeInPath before you can use a path with a tilde.  So, the end result for ~/Library/Application Support is /Users/user/Library/Application Support/

----

Ideally, The application support directory should be queried by NSSearchPathForDirectoriesInDomains, but unfortunately that does not seem to be the case.

*However, NSSearchPathForDirectoriesInDomains does return Library, and you can manually append Application Support on to that. Note that you do not want quotes, because this is not a UNIX shell.*

*As for the original problem, there is an NSTemporaryDirectory() function that can be used to get a temporary directory.*

