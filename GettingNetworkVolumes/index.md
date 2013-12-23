---
layout: page
title: GettingNetworkVolumes
---

Hi

I've devoured NSWorkspace into giving me a list of paths to all mountedLocalVolumes, this lists everything from the hard disks to cds to network volumes. NSWorkspace has another option to list the paths of mountedRemovableMedia. This will give ipods/firewire drives, network volumes and cds. However, I want to be able to single out certain volume types, namely being network volumes. I want to be able to get a list of hard disks, a separate list of removable media, and then a separate list of only network volumes. NSWorkspace unfortunately wont allow this. Is there any possible way of doing this? I have done it in Applescript Studio so it is most definately possible in cocoa.

Thanks, 
Cheers

----

I hope someone will chime in with a better way to do it, but how do you get this done in Applescript?  It can likely be called from cocoa.

----

This script will give you 3 lists.
� Hard Disks
� Removable Media
� Network Volumes

    
tell application "System Events"

-- to get any hard disks etc		
set hard_disks to (name of every disk whose local volume is true and ejectable is false) as list

-- to get any removable media		
set rmv_media to (name of every disk whose local volume is true and ejectable is true) as list

-- to get any network disks	
set net_volumes to (name of every disk whose local volume is false) as list

end tell


Just wanna do this in cocoa. At the moment all I can get is one full list of every mounted volume local or unlocal.

-- Louis Klaassen

----

Okay, here's the direct conversion.  It's going to use KFAppleScript handler additions from http://homepage.mac.com/kenferry/software.html though you can do without this addition with a little more effort.  It's mainly used to convert the applescript lists of strings to NSArrays of NSStrings.

Typed in the browser window, so please correct as necessary.

Save the following in volumeInfo.scpt 
    

on hard_disk_names()		
   tell application "System Events"
      (name of every disk whose local volume is true and ejectable is false) as list
   end tell
end hard_disk_names

on removeable_disk_names()
   tell application "System Events"
      (name of every disk whose local volume is true and ejectable is true) as list
   end tell
end removable_media_names

on network_disk_names()
   tell application "System Events"
      (name of every disk whose local volume is false) as list
   end tell
end network_disk_names



Now, in the cocoa program,

    

NSDictionary *errorDict;
NSAppleScript *volumeInfoScpt = [[NSAppleScript alloc] initWithContentsOfURL:*<url to saved scpt file>* error:errorDict];

// do error processing

NSArray *netDiskNames = [volumeInfoScpt executeHandler:@"network_disk_names"];
// etc.



It's likely that paths will be of more use to you than names.  Alter the script accordingly if this is the case.

----
Are you by any chance in Australia? Or close by? The timing of our posts seems way too close for you to be over seas :P

Anyhow, thats cool, and this method is even better because it ties in with how my tables work. Pity the whole project couldn't have been cocoa, as it was the original aim. I already had made half of the app in Applescript Studio, but AS failed when it came to table views and it was just really slow and buggy. So I decided to re-write the entire project in cocoa, and using just little snippets of Applescript wont matter too much. However I am still open for completely cocoa solutions. With this code I could probably adopt most of my old AS stuff.

Btw, are there any other project settings I need to change for the Applescript to work?

----

*Are you by any chance in Australia? Or close by? The timing of our posts seems way too close for you to be over seas :P*

Nope, north america.

*Btw, are there any other project settings I need to change for the Applescript to work?*

Only make sure you #import "KFAppleSciptHandlerAdditionsCore.h" in each file where you want to use -[NSAppleScript executeHandler:].  There's an NSAppleScriptEnabled plist key you may be thinking of, but that's used when you want to signal that your app is scriptable, not when you want to run scripts.

If you're going to put the volume names in a table, it might be noticably slow to run the script every time the table wants data.  If that's what you find, you can cache the names in a local variable and dirty the cache when you receive NSWorkspaceDidMountNotification or NSWorkspaceDidUnmountNotification.

