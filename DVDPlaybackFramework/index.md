---
layout: page
title: DVDPlaybackFramework
---

There seems to be very little example code on how to use this. Does anyone have a link to something I can look at as an example?

Is there some documentation of the DVDPlaybackFramework?

----

Try http://developer.apple.com/samplecode/CocoaDVDPlayer/CocoaDVDPlayer.html

----

There are some documents and a couple sparse examples from the Panther days, but these won't work well in Tiger. A few of us are trying to use it (see below), and there's not a lot of clear info on problems with Tiger. Apple doesn't offer a lot of strong info on how to use DVDPlayback.framework.

And today, look what showed up: DVD Playback Services Programming Guide. http://developer.apple.com/documentation/GraphicsImaging/Conceptual/DVDPlaybackGuide/index.html

----

**Earlier Discussion**

I've been playing with the DVDPlaybackFramework lately, and I happened to get this weird situation, and I don't know how to fix it:
The DVD plays, and I know it is because I can tell it's spinning. I've been using a window to try to display the video, and that doesn't work. However, the sound works. Here's my code:

    

DVDInitialize();
	
	CGDirectDisplayID displays[MAX_DISPLAYS];
	CGDisplayCount displayCount;
	
	DVDSetVideoWindowID([mainWindow windowNumber]);
	
	NSRect boxFrame = [displayBox frame];
	CGRect cgr = { {NSMinX(boxFrame), NSMinY(boxFrame)}, {NSWidth(boxFrame), NSHeight(boxFrame)} };
	CGGetDisplaysWithRect(cgr, MAX_DISPLAYS, displays, &displayCount);
	DVDSetVideoDisplay(displays[0]);
	
	Rect nr = convertCGRectToQDRect(cgr);
	
	DVDSetVideoBounds(&nr);
	
	FSRef fsref;
	CFURLGetFSRef((CFURLRef)fileURL, &fsref);
	if ([fileURL path] lastPathComponent] isEqualToString:@"VIDEO_TS"])
		[[DVDOpenMediaFile(&fsref);
	else
		DVDOpenMediaVolume(&fsref);
	
	DVDPlay();



This is in my controller's awakeFromNib method, and it's simply using a window ("mainWindow") and a view drawn in IB ("displayBox"). Nothing special. The fileURL points to a disk in the drive. Again, I know it's working somewhat, since I can hear it, but not see it.
There have been a few other people who have had problems with the Tiger version of DVDPlayback framework, but I haven't been able to find out whether they resolved their issue or not.

----

I messed with it a bit in the 10.2 days and only ever managed to get to the main menu. The ads played fine (sound and video) but I never managed to get the movie going; It wouldn't accept clicks or key presses for some reason. Anyway...

I suspect that the few people that know how this works haven't checked these pages out because they've already got a working solution.

----

I did find a working example - actually 2 working ones, but it only works in Panther. Something broke these examples in Tiger. Apple changed something about the way it displays with DVDPlaybackFramework in 10.4. One example at http://niceplayer.indyjt.com/plugins.php and the other is at http://www.zathras.de/programming/cocoa/MovieTheatre.zip

They will accept clicks by implementing the event handling, but it's hard to use them when you can't see them.
In these examples, they use a custom view or some other view object to get the bounds to display in, and then tries to play through that. At least, that's what I'm seeing.

----

I am trying to create a kiosk in cocoa and I want to be able to play a dvd from within my kiosk app.  What is the best way to go about playing a dvd?  Are there any particular API's I should look at for this?   I have dug through the Cocoa docs and have not found anything yet.

----

Well Cocoa can't do everything, can it. You should be looking into QuickTime or OpenSource libraries (check out what VLC uses).

----

VLC uses patented decoding methods (as must all DVD players), which makes its techniques illegal to use in the US without a license from the patent holders. Also, VLC uses code to crack the encryption found on the vast majority of DVDs which is illegal under the US's DMCA. So this is not a good approach to take if you plan on doing anything in the US.

Theres a framework built-in to OSX that lets third party apps play DVDs, its called DVDPlayback.framework

----

Thanks.  I am taking a look in the DVDPlayback .h file right now.

.... I've looked at DVDPlayback.h, and I don't understand it. Is there example code for this somewhere?

----

What don't you understand? There's a comment in the header which explains the steps you need to take.

----

There's an example on Uli Kusterer's site called "MovieTheater" (http://www.zathras.de/programming/cocoa/MovieTheatre.zip), but the example doesn't show a picture in the latest versions of OS X. Is there a more functional example of this framework online? From what I read, it looks like it requires some knowledge in Carbon...

----

I've been reading all of the framework documentation, and I made use of the code from the DVD player example code that Apple provides, but I'm unable to figure out one little feature that Apple's regular DVDPlayer app has: video clips. They have bookmarks, which are used in the example code, but I have no clue how to implement the video clip feature, which marks a beginning and end point. The documentation hints that it's doable, but how?

----

It appears that little has changed with Leopard. They've deprecated some of the older stuff, but I still can't find the answer to my question: can I somehow render the DVD image without specifying a window, so that I can throw the image onto a CoreAnimation layer? I asked this question of a couple guys at WWDC, and never really got a solid answer (I may have been wording my question incorrectly). -- JasonTerhorst

----

It's possible that you can't for copy-protection reasons.  If you could shuttle things off into a CoreAnimation layer, you could potentially hook into OpenGL and steal the frames that way.

----

I'm having problems with undocumented error codes from the DVDPlaybackFramework in Leopard. DVDPlayback.h has these error codes:
    
kDVDErrorInvalidBookmarkVersion		= -70030,	//	invalid bookmark version
kDVDErrorInvalidBookmarkSize		= -70031,	//	invalid bookmark size
kDVDErrorInvalidBookmarkForMedia	= -70032,	//	invalid bookmark for media
kDVDErrorNoValidBookmarkForLastPlay	= -70033	//	no valid last play bookmark


But the Framework reference (http://developer.apple.com/documentation/GraphicsImaging/Reference/DVDPlaybackRef/Reference/reference.html#//apple_ref/doc/uid/TP40001864-CH4g-RCM0080) stops at -70029 in it's result codes section. I keep getting the kDVDErrorInvalidBookmarkVersion (-70030). Has anybody else had any experience with this error? I'm trying to save DVD bookmarks and then recall them later to play arbitrary clips from a DVD.

----
My guess would be that "version" is a way for Apple to enforce having only one bookmark per DVD, exactly to prevent what you're doing. (MPAA sucks, etc.) Do you get this error if you save and use only a single bookmark? If the error only happens when using multiple bookmarks then I'd guess that would be the problem.

----
It's been a while since I've messed with the framework, but I remember that I found just making my own bookmark class, which stored the time, frame, title, etc. was more useful to me than the bookmark type that Apple provides. I also had my own reasons (marking "in" and "out" points, etc.) for using a class. Once I got beyond the weird datatypes (double, float, time value...), it was easier. -- JasonTerhorst

----
As far as I know, the sole reason for using the framework's bookmarks is that they allow you to bypass no-skip portions of the disc such as the FBI warning, whereas the framework won't normally allow you to seek while those are playing.

----
I'm also using an in/out point type of system, but I moved to DVD bookmarks because we ran into problems with language tracks, subtitles, etc not being properly restored from our homemade "fake bookmark" system. I've also recently discovered that the -70030 only pops up when I try to save a bookmark with the DVD paused. Apple's DVD Player doesn't have this limitation, even though it supposedly uses the same framework. I was wondering if anyone else has had this problem.

----
It appears that in SnowLeopard, it's not possible to attach a debugger to an app with the DVDPlayback.framework active. Anyone have tips for getting around this?

----
It's not just SnowLeopard, you can't run an app in the debugger if the DVD Playback framework is loaded in any OS. This is to protect the copyright protection code in the framework from reverse engineering. If I recall correctly, the debugger will exit with a status of 45 in this scenario. There is a debug version of the DVD playback framework available in the debug libraries for 10.5 (see http://developer.apple.com/library/mac/#technotes/tn2004/tn2124.html) however, there are no debug libraries for 10.6. In our application, DVD playback was a secondary feature so we disabled that entire part of the code whenever we were running in debug mode. This works for debugging other parts of the app, but if you have to debug DVD playback on 10.6, you are probably going to have to resort to CavemanDebugging. Another option is to keep a 10.5 install around for debugging DVD stuff.

----

Just checked into this further (on Aug 2, 2011), and it appears there is some new-ish documentation to help under the "Debugging" section here: http://developer.apple.com/library/mac/#documentation/GraphicsImaging/Conceptual/DVDPlaybackGuide/dvdguide_tasks_2/dvdguide_tasks_2.html#//apple_ref/doc/uid/TP40002163-TP40003220-TPXREF101
- JasonTerhorst

