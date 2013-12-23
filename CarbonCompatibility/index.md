---
layout: page
title: CarbonCompatibility
---

This is a list of classes by different authors that provide functionality that is not available as Cocoa Objective-C classes from Apple, for example Carbon technologies.

This is being integrated with OtherAPIs and CocoaWrappers.

----
**Resource Manager**
----
Description: The Resource Manager is a Carbon manager that deals with the resource fork and as of late also resources stored in the data fork (.rsrc files).

Classes:

***OFResourceFork, OFResource** - Part of OmniFoundation, Authors: OmniGroup
***NDResourceFork** - http://homepage.mac.com/nathan_day/, Part of NDAppleScriptObject, Author: Nathan Day
***GTResourceFork** - http://www.ghosttiger.com/?p=117, Author: JonathanGrynspan

Functionality present in classes:

* Resource file reading, modification


Functionality missing from classes:

* Advanced control over resource access, modification and deletion
* Specialized tools for dealing with different types of resources


----
**FilesystemNotifications**
----
Description: Filesystem notifications allows applications to know when another process made a change in the filesystem, and reflect that.

Classes:

***DRFileNotificationSubscription** - http://ittpoi.com/drfns.tgz, Author: DavidRemahl
***GTSubscription** - http://www.ghosttiger.com/?p=132, Author: JonathanGrynspan

Functionality present in classes:

* Registering for notifications


Functionality missing from classes:

* Posting notifications is present in NSWorkspace instead.


----
**Alias Manager**
----
Description: Alias Manager deals with aliases which represent files and can track them even as they move, change name etc.

Classes:

***NDAlias** - http://homepage.mac.com/nathan_day/pages/source.xml, Author: NathanDay
***BDAlias** - http://bdistributed.com/Projects/BDAlias/, Author: ChrisHanson

Functionality present in NDAlias:


* converts to/from paths (NSString), urls (NSURL), FSRef, and FSSpec
* conforms to NSCoding
* can read and write alias files
* provides alias-related categories on NSOpenPanel, NSPathControl, NSString, and NSURL
* garbage collection support
* 32 and 64 bit support


Functionality present in BDAlias:


* Creating Alias Records and getting data that can be saved persistently
* Resolving of Alias Records to paths or URLs


Functionality missing in BDAlias:

* Resolving of Finder alias files



----
**KeyChain Manager**
----
Description: The KeyChain is an API for save storage of user passwords and other authentication information.

Classes:

***Fire KeyChain** - http://www.sourceforge.net/projects/fire/, Author: Fire Development Team (Mainly Colter Reed)
***SecurityHICocoa.framework** - Private OSX Framework, Author: Apple Engineers
* **KeyChainItemLabel** - shows the two main functions used to store and retrieve a password
* **Keychain Framework** - http://sourceforge.net/projects/keychain/

Functionality present in classes:

* Storing and retrieving passwords


Functionality missing from classes:

* I don't know...Find out and write it here.


----
**HotKeys**
----
Description: The Hot Keys API allows applications to catch key events that happen in other applications. This is useful if you want a particular event to activate your app, or toggle some action globally on the system

Classes:

***SBHotKeyCenter** - http://rogueamoeba.com/sources/HotKeysLib3.zip, Author: Quentin D. Carnicelli ( as mentioned in this post: http://www.rogueamoeba.com/utm/posts/Random/Homegrown_Developer_Tools-2004-07-14-12-00 )
***NDHotKeyEvent** - http://homepage.mac.com/nathan_day, Author: Nathan Day

Functionality present in classes:

* NSApplication subclass that forwards events to a SBHotKeyCenter (only if you still need 10.1 support)
* The center can forward hot key combinations to any target/action after registration to receive them

