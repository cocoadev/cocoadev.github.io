---
layout: page
title: ApplicationSupportFolder
---

The Application Support folder is, according to Apple, to be used for any data that supports an application but is not required for it to run. Each application should create its own folder within the Application Support folder. The files should be application-specific but should *never* store user data.

In practice, the Application Support folder has been used and abused in quite a lot of ways, sometimes including user data.

http://developer.apple.com/documentation/MacOSX/Conceptual/BPFileSystem/Articles/WhereToPutFiles.html

To find the location of the Application Support folder, use NSSearchPathForDirectoriesInDomains (on Mac OS X v10.4 or later) or Carbon's FSFindFolder. 

Application Support folders are located within the various Library folders on the system (    /Library,     ~/Library, etc.). Application plug-ins, according to Apple, should be stored within a Plug-Ins subfolder of the application's support folder.

Unfortunately, there are no naming guidelines (such as the reverse-DNS-style bundle identifiers) to prevent conflicts. The de facto standard is to use the name of your application as the name of its support subfolder in the Application Support folder.

----

A discussion, a question, some code and a of question concerning the use of the Application Support folder.

Many applications use the Application Support folder in an assortment of unintended and unwise ways. They know who they are and what they're doing, so there's not reason to beat that dead horse any further. Additionally many applications violate Apple's usage guidelines for the Applications folder as well. I would like my latest application to  do its best to be a good resident of the user's computer, including respecting multi-user setups.

In my application there is a game board and a set of pieces. There are several default board styles and piece sets that are contained within the application's bundle. I believe this is the proper location for them as I do not want them to be easily edited/removed/lost. I do want the particularly motivated user to be able to able to create and possibly distribute their own boards and/or piece sets. It is my understanding that creating a directory, ~/Library/Application Support/MyApplication-version/, to hold these user additions would be acceptable usage. I'm modeling this on the way apps like OmniGraffle use the Application Support folder to hold custom stencils for example.

As a side note, I have chosen to suffix the folder with a version number in the hopes of avoiding potential naming conflicts, since the Application Support folder has not been redone in the manner of preference files, i.e., com.apple.blah.blah. It's not a perfect solution, but it's as good as I feel like implementing. Some sort of cryptic unique naming would merely make it harder for the user to work with, IMHO, without any substantial conflict resolution benefit.

The structure would be something like:

    
 ~/Library/Application Support/MyApplication-version/
 
 ~/Library/Application Support/MyApplication-version/Boards/
 
 ~/Library/Application Support/MyApplication-version/Piece Sets/


On first-run, the empty directories would be created, perhaps with a text file in each explaining their purpose. It is my intention to create these in the User's domain and not in Library/Application Support/. I'm not quite sure if I should even/how I should notify the user that they can make their Boards/Piece Sets accessible to all users of the computer by moving them to that location. Perhaps that's best to put in some documentation.

Each time it is run the application would check both     ~/Application Support/MyApplication-version/ and     Library/Application Support/MyApplication-version/ at startup for custom Boards/Piece Sets and make them available.

So my first open question is: 
What are people's opinions on the use of the Application Support for this type of purpose?

Part of the implementation of this idea is the being able to find the Application Support folder. For that, I'm using some free code I found on a mailing list a while back. FWIW, I changed the prefix to my own, not as a claim of ownership/authorship but for aesthetics in my own project. It was AMA or something similar instead of PR if you're curious.

NSFileManagerPRAdditions.h
    
 #import <Foundation/Foundation.h>
 
 
 @interface NSFileManager (PRAdditions)
 
 - (NSString *)findSystemFolderType:(int)folderType forDomain:(int)domain;
 
 @end



NSFileManagerPRAdditions.m
    
 #import "NSFileManagerPRAdditions.h"
 
 
 @implementation NSFileManager (PRAdditions)
 
 - (NSString *)findSystemFolderType:(int)folderType forDomain:(int)domain
 {
     FSRef folder;
     OSErr err = noErr;
     CFURLRef url;
     NSString *result = nil;
 
     err = FSFindFolder(domain, folderType, false, &folder);
     if (err == noErr) {
         url = CFURLCreateFromFSRef(kCFAllocatorDefault, &folder);
         result = [(NSURL *)url path];
     }
 
     return result;
 }
 
 @end

*(at some point,     CFRelease(url) should be called to release what was created, otherwise you're leaking memory -Seb)*


usage examples:
    
 // find the path to ~/Library/Application Support/
 NSString *applicationSupportFolder =
     NSFileManager defaultManager] findSystemFolderType:kApplicationSupportFolderType forDomain:kUserDomain];
 
 // find /Library/Application Support/  <--- not sure if I can just assume it's always at that path
 NSString *applicationSupportFolder =
     [[NSFileManager defaultManager] findSystemFolderType:kApplicationSupportFolderType forDomain:kOnSystemDisk];


I believe you should/would need to check that the value is not nil before doing something with it, just in case no folder exists in the domain specified.

Obviously this is Carbon code mixed into a Cocoa app, which leads me to my second question:
Is there a simple way (or for that matter a good reason) to replace this Carbon code with something done in Cocoa, perhaps using [[NSPathUtilities?

NedO

----
(1) Sounds like a good use to me.  What are the abuses of the Application Support folder you refer to?  They may know who they are and what they're doing, but I don't. ;-)

(2) There is nothing wrong with using Carbon in Cocoa, and I've never seen a pure cocoa method for obtaining this directory.

(3) I would not append -version to your directory name in the application support folder.  Version individual files (pieces and board) instead.  Are you trying to protect against future changes in hierarchy of the support folder?

The OmniGroup used a separate folder for OmniWeb 5, but I don't see that it's the same situation.  At the time of release, OmniGroup wanted people to be able to switch back and forth between OmniWeb 4 and 5, and it wouldn't have been acceptable for 4 to ignore a file like the bookmarks (don't know if this particular file was changed) that was of a newer version than OW 4 could deal with.  For you, you probably want your program to continue to read older versions of piece and board files after you've introduced a new version, and it is acceptable for a downgrading user to lose access piece and board files should that be necessary.  You may even be able to write your file formats such that newer files are still readable by older versions.  If a major change is necessary that invalidates the previous, there's nothing to keep you from using an application-new_version folder at that time.

The disadvantages of using the version in the folder name are that either (1) an upgrading user must manually move files from previous support folders to new ones, or (2) you have to programmatically check old support folders for files to import.  If using the second option, this can either occur at every startup, or at a prompt from the user.  Neither are ideal.

----
Cocoa has     NSSearchPathForDirectoriesInDomains() which is mostly the same as     FSFindFolder(). However, it has many fewer folder constants, and importantly for this page, there is no constant for the Application Support folder %%*prior to 10.4, which added some new constants*.

----
Good comments about the version tag. I had thought to use it for uniqueness more than anything, but I didn't even consider the implications on upgrades. I agree it is probably best to toss that out unless the versions reflect a change in Board/Piece Set format.

Here is Apple's statement regarding what should go in the Application Support folder: (from the ADC, search for Library Directory, its a giant URL).

"Contains application-specific data and support files such as third-party plug-ins, helper applications, templates, and extra resources that are used by the application but not required for it to operate. By convention, all of these items should be put in a subdirectory named after the application. For example, third-party resources for the application MyApp would go in Application Support/MyApp/. Note that required resources should go inside the application bundle itself."

*Steps up onto soapbox*

Unsuitable crap I find in my Application Support directory:

1) User prefs. (Unfortunately common) - Why not?

(a) Hoses backups. Do I backup the app or the app and the Application Support folder or both or what?

(b) Hoses security. Or put another way, how to beat early versions of LittleSnitch.

(c) Hoses read-only media. No network apps.

(d) Hoses the developer. Developer has to add support for writable media(for no good reason).

(e) Hoses filesystem optimizations

(f) Hoses upgrading (for the reasons you listed to avoid versioning the Application Support and more)

2) Cache files, activity logs and usage stats - of very questionable acceptability

3) File backups - Can we say Documents folder?

4) Required components (palette elements, etc.  without which the app is useless/nearly useless) - Install app as one user. Then log out and log back in as another and try to run the App. Oops!

While this folder is not as bad as the old pre-OS X Preferences folder days, it can get messy. The Preferences folder used to turn into a giant garbage pile and unfortunately some programs commercial, shareware and otherwise continue this bad practice today with the Application Support directory. I really don't want to name names but a decent place to start is to look in the folder under Macrom...., MacJour....., Azur....., Freev....., etc. if you have their software that is.

This obnoxious trend seems to have been supplanted in OS X by the trend of requiring root privileges to install, pointless restarts, admin access to run and unreasonable write access requirements.

*Gets down off of soapbox*

NedO

----
Good behaviour would be to create a folder in Application Support if and only if it's really, really, really needed, and never rely on the folder being there unless it is essential for your app to write down things.
If the Application Support folder is only needed for user-supplied plugins and whatnot, you might document the fact that such a folder must be created *but* never create it yourself -- merely test its existence. It's up to the user (or the third party Installer package) to create the folder.

----
I second that!!! I run many applications just once (to test them), and I don't want them cluttering my AppSupport -- furthermore, something like Terminal.app now (on Tiger) creates an empty support folder, and while I do use Terminal.app, that folder is empty, and it messes up the shells filename completion (previously I could do T<tab>, now I need to do Tex<tab> to get to the TextMate support folder). Btw: something which hasn't been mentioned, the application bundle can have a Contents/SharedSupport which seems to be the equivalent of a default AppSupport folder. E.g. if you ship your app with default templates or similar, place them in SharedSupport.

----
how is SharedSupport shared? you could always put templates in your app bundle wherever you wanted to.

----
There is a method to get to the SharedSupport directory, and this is what Apple writes:
*A SharedSupport directory contains additional non-critical resources that do not impact the ability of the application to run. For example, this directory might include things like document templates, clip art, and tutorials.*
I don't know why they choose the 'shared' prefix, and of course you can put stuff wherever you want, but please stick to the guidelines whenever possible. I assume one advantage is that if I'm out of space, I could theoretically wipe all the shared support in various applications.

----
Hmm... I notice Tiger's Finder lets you drag things *onto* .app icons. Is this a bug? Or drag n drop plugin installation? (eg drag a plugin onto the app package icon, it gets routed to SharedSupport inside the bundle)

2) should go in the temporary files folder (the OS clears it on reboot). 3) should go in Documents. 4) should *never* *ever* leave the app's bundle (that's why bundles exist, by the way).

----
Sample Code to get ~/Application Support folder path

    
 - (NSString *)applicationSupportFolder {
     NSString *applicationSupportFolder = nil;
     FSRef foundRef;
     OSErr err = FSFindFolder(kUserDomain, kApplicationSupportFolderType, kDontCreateFolder, &foundRef);
     if (err == noErr) {
         unsigned char path[PATH_MAX];
         OSStatus validPath = FSRefMakePath(&foundRef, path, sizeof(path));
         if (validPath == noErr)
         {
             applicationSupportFolder = NSFileManager defaultManager] stringWithFileSystemRepresentation:path length:(NSUInteger)strlen((char*)path)];
         }
     }
     return applicationSupportFolder;
 }


And to append your application name:

    
 applicationSupportFolder = [applicationSupportFolder stringByAppendingPathComponent:@"PROJECTNAMEASIDENTIFIER"];


----
I created a abstract class for managing a application's application support folder.
It provides a easy way to see if the support folder is created, create the support folder, and get the support folders path. Download: http://developer.mabwebdesign.com/getfile.php?file=46
-[[MichaelBianco

----
Seems like a lot of work when the equivalent FSFindFolder code is about three lines long.

