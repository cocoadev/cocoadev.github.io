---
layout: page
title: ReadingITunesPlaylists
---



What would be the proper way to integrate with iTunes playlists, so that I can get a list of songs/playlists in my application? Ie. like iLife apps.

*Read the xml file.*

----

The first thing to look at is the com.apple.itunes.plist file in the user's library/preferences folder. Locate the library xml file (which is in the plist format and can be loaded with the NSDictionary's initWithContentsOfFile: ... Examine both these files with a preference list editor to determine their structure. Consult the documentation and this site to figure out how to load and manipulate files of this format.

----

Hmm, you could do this, but I'd probably use NSAppleScript to directly query iTunes.  Unless it's something where the speed hit is a problem (but don't assume it will be a problem - it's fast enough for lots of stuff).

----

Depends on what you want to do. I suggest the above if speed *is* an issue and you want to get *all* the details. It took me no time at all to understand the format and to build an engine to parse it (and extract the information I needed). It took slightly longer to get everything straight in my head for coding, of course, but ... ;-)

----

The main difference in my mind is that the AppleScript API is documented, the plist layout is not.  They don't care if they break the latter in a future version of iTunes (what if they move to CoreData sqlite?), where for the former they at least try to keep the interface the same between versions. 

----

The XML file is specifically for use by third-party programs. iTunes itself only uses the proprietary database. Also, building an engine to parse the XML file is totally superfluous; a simple [NSMutableDictionary dictionaryWithContentsOfFile:[@"~/Music/iTunes/iTunes Music Library.xml" stringByExpandingTildeInPath]] is totally sufficient. The tracks are under key "Tracks", the playlists under "Playlists". Try to open the file in Property List Viewer.

----

Excellent, thanks to everyone for their input... one thing was missed though, using NSAppleScript would require iTunes to be running, and this is not always wanted (at least not in my case). So I will use the XML file :)

One more thing, if I may:

The Location in the XML file for each song is specified as such:

file://localhost/Users/zigzag/Music/iTunes/iTunes%20Music/Babble/The%20Stone/10%20Drive.mp3

Is there a good, proper way to get this into a file path? Ie.

/Users/zigzag/Music/iTunes/iTunes Music/Babble/The Stone/10 Drive.mp3

----

*
-[NSURL path]
*

----

"The first thing to look at is the com.apple.itunes.plist file in the user's library/preferences folder"

I don't see any information pertaining to the location of the XML file in the iTunes prefs??

**Yeah, there's not. It's just a standard place to look, and the poster was probably saying that you should look here first. Although my iTunes' plist file has a key, "    alis:11345:Music Folder Location", that looks suspiciously like an encoded file object...but who knows what it is? --JediKnil**

*I am the poster of that comment ... There *was* a key there, honest. ;-) My code still looks for it. Failing that, it looks in the usual place (~/Music/...). Apparently that key is no more.*

To get the location of the user's iTunes music library, you just need this (warning - typed in Safari):
    
NSString *pathToiTML = nil;
NSDictionary *itml = [NSDictionary dictionaryWithContentsOfFile:[@"~/Music/iTunes/iTunes Music Library.xml" stringByExpandingTildeInPath]];
if (itml)
    pathToiTML = NSURL [[URLWithString:[itml objectForKey:@"Music Folder"]] path];


----

It seems like Apple changed the way to store the iTunes database.  Is there any way to read the "iTunes Library" file?  I've tried many ways, but i just couldn't read anything.  Suggestions? --LucaC

