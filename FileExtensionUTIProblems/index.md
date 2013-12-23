---
layout: page
title: FileExtensionUTIProblems
---

I have recently written a Viewer and a Spotlight Plugin for a filetype that I work with frequently.  These files have two possible file extensions(unfortunately I cannot reveal the filetype and extensions, so for the sake of this page I will call the extensions: MICK and MCK).  In both my viewer and mdimporter Info.plists I have added both these extensions as well as the UTI I wanted to apply to them, let's call it "com.mycompany.mick".

I have two accounts on my machine; one for work and one for home.  On my home account the Spotlight Plugin correctly indexes both MICK and MCK files.  Also when I get "More Info..." from the Finder, it reccommends my viewer for either type of file.  On my work account the Spotlight Plugin only indexes MICK files and the same "More Info..." interaction does not suggest my viewer as a recommended type for MCK files, only for MICKs.

On my home account running 

    /usr/bin/mdimport -d2 myfile.mick

and

    /usr/bin/mdimport -d2 myfile.mck

returns the type of 'com.mycompany.mick' as it should be and that kMDItemKind = {"" = MICK; };

However, on my home account running the same two commands returns the correct type for MICK files, but not MCK files.  It also claims that both are kMDItemKind = {"" = MICK; }; just as in my home account.

So, it appears that in my work account somehow MICK and MCK files have both correctly received the 'com.mycompany.mick' UTI and my work account has only associated the MICK extension to the UTI.

Doing the same tests on a colleague's machine yields the same result as my work account (ie - the wrong result).

My question, therefore, is how do I associate both MICK and MCK with my UTI?  Somehow it occurred on my home account, but I do not know how.

----

I have solved my own problem, and am posting the solution for others who may encounter this.  The problem was that LaunchServices had associated MCK and MICK with dozens of apps(many of which didn't even exist anymore).  Each time I made and ran a version of my app from a different location, it was included in the launch services database.  So some other version of an app was claiming MCK as a different UTI, and not allowing my new app and spotlight plugin to claim it.  I found a command that wipes out the LaunchServices database and builds it fresh from apps currently on your computer.  This in turn corrected my spotlighting UTI problems. 

The command is: 
    /System/Library/Frameworks/ApplicationServices.framework/Frameworks/LaunchServices.framework/Support/lsregister -kill -r -domain system -domain user 

To see what is currently registered in the database you can run: 
    /System/Library/Frameworks/ApplicationServices.framework/Frameworks/LaunchServices.framework/Support/lsregister -dump | less

