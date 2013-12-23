---
layout: page
title: InternetConfig
---



InternetConfig was originally a 3rd party library written by Peter Lewis for MacOS which stored various preferences which ought to be common for various internet apps. Apple adopted the standard after it became widely used. The only major differences in Mac OS X is support for bundles as well as the lack of a configuation panel for the friggin file mappings. Fortunately some Carbon internet apps like Explorer can configure these settings.

You should use InternetConfig if you're importing files from the internet or wherever. It has ONE SET of MIME<->filename extension<->HFS type mappings. If you don't use InternetConfig you will be using your own set of preferences which the user will have to update separately. That's a royal pain in the ass, I went through that crap with Netscape 4.x and earlier which didn't use IC. Thankfully Netscape was the exception and not the rule.

The major hassle with InternetConfig for Cocoa developers is the fact it uses Aliases instead of file paths. I consider this a plus since it doesn't break when I change paths by moving or renaming things on my filesystem. Another problem is the InternetConfig API isn't available in a Cocoa form. If somebody wrote one please add it here.

Unfortunately Apple has yet to support MIME types at the filesystem and API level like BeOS did. Instead they decided to regress by using filename extensions. The advantage of MIME types is you have a level of abstraction whereby files aren't tied to applications, but rather MIME types which then can be bound to an application. Thus you can have a set of JPEGs which have one secondary type (like "photoquality") and another set of JPEGs with another secondary type (like "webquality") then map one type to one app and the other to another app. The fact that Apple didn't adopt MIME means you have to import and export MIME types when sharing files. Adding to the problem is Apache which uses a separate means to map filename extensions to MIME types (and ignores HFS types).

-PaulBayley

----

I read that LaunchServices is set to take over the functionality provided by InternetConfig at some point in the future, but that the APIs for this are not yet public. -- FinlayDobbie

----
There is a preference pane for file mappings, called MoreInternet, at http://www.monkeyfood.com/software/MoreInternet/

----

InternetConfig doesn't have just one set of mappings. You're allowed to define as many mappings as you desire between MIME/file extension/MacType/application. In InternetConfig, you could have ten different mappings for, say, .jpg, all valid. InternetConfig then guesses which one you want, or you get to guess by slogging through the database.

In terms of the file system, the MIME vs. extension (vs. MacType) debate amounts to 6 of one, half a dozen of the other. Either way it's a mapping between a file and applications which *could* open or otherwise use it. If you download a .jpg file, any number of applications could open it, which is why in Cocoa we define document types. LaunchServices figures out which apps can open which files by looking at app bundles' plist(s), and gives the user as many options as it can.

Also, where in MIME is there a place for 'webquality?' image/jpeg/webquality?

----

The Mappings part of IC is not really meant to be used under OS X.  It is a legacy from OS 9.  HFS FileTypes are not really the "OS X way" as far as Apple is concerned.  Only Carbon ports from OS 9 tend to use the Mappings to add HFS metaData (creator/fileType) to downloaded files.   The OS X way is to just have file extensions and let LaunchServices take care of which app should launch it.

The part of IC that we are still reliant on is the URL Schemes (and the location of the download folder)

Most cocoa programmers won't need to read the URL schemes because NSWorkSpace takes care of all the Protocol Mappings for you when you launch an NSURL.

You would need to use IC to alter the URL schemes - i.e. Bind your App to a specific protocol (like iCal to iCal://  or some FTPApp to ftp://)

I would suggest against this though - Internet Config is a system-wide preference - and therefore should not be changed "quietly" by an application (remember all those Battles in OS 9?  Every time you launched an internet app you'd get: "FooApp is not your default FooBrowser - I'll be really helpful and set it for you.")

I'd suggest that the correct place for changing system-wide prefs is in SystemPreferences - Unfortunately Apple didn't expose all the functionality.   That's why I wrote the More Internet Pref Pane
--DiggoryLaycock

