---
layout: page
title: SideBarContent
---



How can I get the sidebar content from inside Cocoa ?
The info is shared between the Finder and the Open/Save panel,
so it must reside in some database...
What process controls that info ?  coreserviced ?

----

*I think its just alias data inside of a plist somewhere. Have a look at ~/Library/Preferences/com.apple.sidebarlists.plist*

----

.plist file seems to be only updated when Finder exits and/or you set the preference panel.
When you drag some item in the Finder's sidebar and if you have an alive Open/Save panel
in some other app, its sidebar is updated immediatedly! 
and it seems to be done by /usr/bin/distnoted (try killing it.)

*use NotificationWatcher then, and see if a distributed notification is posted when the sidebar changes*

*...a day passes...*

*Yes, a distributed notification is posted when the sidebar changes.*

Thank you, I checked that.  OK then we can get the changes, but how can we get the original sidebar content
when I start up an app ? Should I be content with the plist ?

----

A related issue is that my sidebar often shows some sticky volumes
which persist even after ejecting them.
BSD layer seems to be well aware of the eject, so what's happening ?

----

*I think this is a OSX bug. Unless you dragged them in. I've never seen it with the sidebar, but it happens all the time in DiskUtility. Check your /Volumes directory. You probably have a bunch of old image mounts in there.*

Just out of curiosity, what are you using it for?

----

Just to document this a little more clearly:

The Finder's sidebar is controlled by a plist in the users directory, namely ~/Library/Preferences/com.apple.sidebarlists.plist. Inside this file are two keys: "systemitems" and "useritems". "systemitems" controls what is displayed in the upper portion of the sidebar (things like HD's, etc), and seems to be tightly controlled by the system (I am not investigating further).

"useritems" is where most of the attention probably should be focused. Inside "useritems" is a Array under the key "CustomListItems". Under this are plist dictionaries with three keys: "Alias", "Icon", and "Name":

*Name: (type String) This is simply the text string that is displayed to the user. This is user-modifiable through the finder.
*Alias: (type Data) This is just as base64 encoded version of the data from an alias. To get this information on the command line you can use this comand:
    
cat <file path>/..namedfork/rsrc | uuencode -m /dev/null

Don't include the line "begin-base64 644 /dev/null", as that is just for the file.

You can also create the alias programatically, but that is for another article.
*Icon: (type Data) This is once again encoded in base64, and is the type/creator/icontype describing the icon. For example the one for finder folders is "ImgR        SYSL        fldr"


Once you have made the changes to the plist that you want you have to announce them with a posting to NSNotificationCenter. Namely one with the name: "com.apple.sidebarlists.<user_id>.useritems", where <user_id> is the numeric user id for the person in question, and a nill object.
----
Ummm, BTW, (almost) anything in     ~/Library/Preferences can be accessed through NSUserDefaults (see example below). This *should* be kept updated with the Finder, and not the plist file. --JediKnil
    
*// Get the defaults as an NSDictionary*
NSDictionary *sidebarListsPlist = [[NSUserDefaults standardUserDefaults] persistentDomainForName:@"com.apple.sidebarlists"];
*// Do some stuff with sidebarListsPlist, then store it back into the defaults (if changed)*
[[NSUserDefaults standardUserDefaults] setPersistentDomain:sidebarListsPlist forName:@"com.apple.sidebarlists"];


----

I want to create a custom sidebar icon for some folders. For example, the Utilites folder.
I have created an .icns file named: ToolbarUtilitiesFolderIcon.icns
and copied it to: /System/Library/CoreServices/CoreTypes.bundle/Contents/Resources/

I understand how to generate the base64code for the Alais, but how does one generate the proper code for the icon?
When I use your example above inserting the location of the .icns file, I get an EXTREMELY long string!

----

After reading all this I found that I don't know how to find what icon the Icon entries point to. "ImgR        SYSL        fldr" is very specific. How do I find the icon for any icon entry?


----

The sidebar favorites are managed by LaunchServices, use the `LSSharedFileList*` methods to manage them. I found this example: https://gist.github.com/nanoant/1244807 extremely useful;

