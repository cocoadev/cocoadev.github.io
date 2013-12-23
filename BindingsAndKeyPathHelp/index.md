---
layout: page
title: BindingsAndKeyPathHelp
---



I'm working on a simple project to mine an iTunes Library file and present the information to the user.  This is mostly just an extended experiment with bindings.  I've managed to link my NSArrayController so that my tableView displays the names of playlists correctly.  Each playlist is an MJPlaylist object with name and NSMutableArray tracks ivars.  

My second column is a count of the songs in each playlist.  I have not been able to get @count to work for me despite any number of strange setups.  I realize that I can simply place a countTracks method in my MJPlaylist class, but it seems as if there would have to be a way to accomplish this with bindings.

You can d/l my project at http://.res-hall.northwestern.edu/files/RMT.zip (about 70k).  Note that if you want it to work, make a copy of your ~/Music/iTunes/iTunes Music Library.xml and make sure it is in the same location as the original and named "iTunes Music Library Copy.xml"

I'd appreciate any help you could offer.  I'd paste code here, but as it's a bindings issue it may be easier to simply look at.

-Michael

----

Your zip file seems corrupt.

----

Just tested it with 2 others, the zip worked fine for them.  Give it another try and let me know.

-Mike

*Change your controller key for the tracks column to "arrangedObjects", not "selection". You're saying 'for reach row in this column, show the selected row's @count.tracks' which of course is wrong. You want to say 'for each row in this column, show THIS ROW'S @count.tracks'. You want the arrangedObjects key, not the selection key.*

That makes perfect sense.  However, when I make that change I get the following message:

    
2005-05-10 12:08:31.799 Rate My Tunes[11208] An uncaught exception was raised
2005-05-10 12:08:31.799 Rate My Tunes[11208] [<MJPlaylist 0x695c3e0> valueForUndefinedKey:]: this class is not key value coding-compliant for the key @count.
2005-05-10 12:08:31.800 Rate My Tunes[11208] *** Uncaught exception: <NSUnknownKeyException> [<MJPlaylist 0x695c3e0> valueForUndefinedKey:]: this class is not key value coding-compliant for the key @count.


Why is my @count method being sent to the MJPlaylist class instead of to the NSMutableArray iVar in that class?

----

Look here:     tracks = [[NSDictionary alloc] initWithObjects:trackArray forKeys:keyArray]; 

*You're misunderstanding the use of an array versus a dictionary here. Your playlist object's     -tracks returns an array (appropriately). An individual track object contains the data (which stands in for a dictionary in this case). You want to ask the array object for its @count. You're creating a *dictionary* of track objects .... bizarrely, I might add. ;-)  Just stop before that and set your playlist's -tracks to trackArray and forget about the dictionary.*

Here is where things get a bit convoluted.  In the case above, I'm actually setting up the tracks ivar of my AppController.  The crazy setup is actually an outcropping of how iTunes keeps track of playlist items in the first place.  The entire set of tracks is stored as a dictionary of dictionaries (with each song's info encapsulated in a dictionary).  Playlists hold an array of dictionaries.  In this case, the dictionaries in the playlist arrays have as their only value the key for a track in the master listing of tracks (the dictionary of dictionaries).  

Looking at my code, I think I'm still correctly setting the tracks ivar of my *MJPlaylist* (it ends up just being an array of dictionaries).  Ultimately, it is a count of the tracks array in each MJPlaylist that I am after.  So, although you're right that it's an odd setup, I think the issue is somewhere else.  Though, I'm definitely no expert.

*If you're trying to directly modify and re-save the XML file, then I can see how keeping that structure is easier for you, however, this will *not* work with bindings as-is. If what you are manipulating is an array of dictionaries. The array being your playlist's -tracks property and the dictionaries being your individual tracks. You could always go more abstract and forget about the custom class for each track - just go with strict KVC ([myTrackDictionary valueForKey:@"rating"], etc.). But you'll have to pick one or the other. I *strongly* recommend going back over the subject of CocoaBindings - you're not quite getting the point, though you're on the right 'track' (pun intended).*

As someone still starting out I'll definitely try to become more familiar with all the resources there.  I've been trying to pick up Bindings piecemeal, which is probably not the way to do it ;-)  If you can forgive my ignorance, would you mind giving me a quick explanation as to why what I'm trying to accomplish won't work as-is?  I figured with my object controller using an array of playlists and the songs controller set to the key-path of playlists that I'd be able to send @count.track to the array represented by tracks....But please, have at it and let me know the error of my ways!

*The only thing I can do is tell you to read about CocoaBindings. Anything I say here would be a complete repeat of things that have been written many other times in many different places. ;-) Not trying to be rude, but I just don't have the drive to reinvent the wheel. Follow the link to CocoaBindings and read what's on this site. Also look for Mmalc's bindings examples - very helpful (zip'd source and xcode project included so you can have a look in IB). If you're having trouble with a technology you're trying to use and have little understanding of the technology, it's a good indicator that you really need to learn the technology before attempting to use it to make something useful. Without spending a whole lot of time looking over your code line-by-line (which I currently have so little time to do for my own projects), it's hard to give you the magic statement that clears it all up for you - and you'll learn nothing. Seriously - dig into bindings, it's time well spent.*

Will do!  Thanks for the help!

