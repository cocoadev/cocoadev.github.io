---
layout: page
title: ProjectBuilderTips
---




Some obvious tips:

Add an RTF file called "Credits.rtf" to your project, and if you use the standard Cocoa 'About' window, the RTF file's contents will be shown in it (in a scrolling text field, if necessary).

Edit the Cocoa project templates here:
/Developer/ProjectBuilder Extras/Project Templates/Application/
to change the default copyright strings (in InfoPlist.strings) to say 2001 instead of 2000, so you don't need to change them in every new application you create.

If you're writing just plain C files, perhaps to compile and run via a shell window, use the "Standard Tool" template in Project Builder.

-- MattRidley

----

Re: Project Templates above

Better than changing the apple supplied templates. Create a mirror of /Developer/ProjectBuilder Extras/ within your home folder and copy the templates you're interested in changing into it. Rename your versions. Here is a list of recognized variables which PB will replace

*DATE  --  current date (using NSCalendarDate format "%x")
*DIRECTORY  --  full path of directory in which the new file is being created
*FILENAME  --  full file name, exactly as typed by the user
*FILEBASENAME  --  file name without extension
*FILEBASENAMEASIDENTIFIER  --  same as FILEBASENAME, but mangled to a legal C-style identifier
*FILEEXTENSION  --  extension of file name
*FULLUSERNAME  --  full name of the logged-in user
*PROJECTNAME  --  name of project in which file is created, "" if no project
*PROJECTNAMEASIDENTIFIER  --  same as PROJECTNAME, but mangled to a legal C-style identifier
*TIME  --  current time (using NSCalendarDate format "%X")
*USERNAME  --  account name of the logged-in user


--DaveHenderson


----

The application name in the menu bar for the application comes from the CFBundleName typically specified in the English.lproj/InfoPlist.strings file in a project.

(**NOTE:** InfoPlist.strings is used to localize string values accessed by the application and other applications such as the Finder when displaying such information about an application as its name, version, etc... The OS first checks for the existence of localized strings files in a User's language of choice in the order set forth in System Preferences. So the file English.lproj/InfoPlist.strings is merely the English language localizations of strings for keys contained in an application's Info.plist.) 

Note that although you can see a CFBundleName item in the "Info.plist Entries" section of the Target window (under Expert View), changing this will *not* change the app name in the menu bar. You have to edit the InfoPlist.strings file.

