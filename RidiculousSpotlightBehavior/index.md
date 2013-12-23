---
layout: page
title: RidiculousSpotlightBehavior
---


Okay, so here's my situation.  I'm building a smallish app to help a client organize a collection of photos.  Never mind the details, but know that a 'regular' solution like iPhoto won't cut it.  ANYWAY, here's the deal.  Through the app, photos can be 'tagged' with small bits of extra data to help organize them.  This data is added both through the XATTR attribute and by adding them to the Finder's 'Spotlight Comment' field.  This tagging is mostly restricted to a categorical sense, so it's hardly complicated.  For instance, a photo might be tagged as "cat1", "cat2", etc.  This classification is designed to be unbelievably simple.

Now here's where the fun begins.  I wanted to make use of spotlight in this app to make searching by tag quick and painless.

It has turned into anything but.  Shocking, I know.  Here's the situation, and please bear with me:

I open the program.  I'm using a test image library of 18 images here, since hundreds just slows down my testing.  This is the chain of events:
*1. Open the program.
*2. Do a spotlight search for images for which I KNOW I tagged as 'cat38'.  The search returns 18 results (as it should).
*3. Edit the image information, and change the data to 'cat39'.  This results in a change in my XATTR attribute and in the Finder's Spotlight Comment (both easily verifiable).
*4.  Following the data update, I run `"/usr/bin/mdimport -f /path/to/entire/image/library`.  This forces Spotlight to Re-index the files.  You'd think, anyway.
*5.  I do a spotlight search for my new 'cat39'.  18 results!! Woohoo, success!

---- Quit the Program ----
In fact, here's my runlog:

    
[Session started at 2006-11-19 02:18:11 -0800.]
2006-11-19 02:18:14.301 Reflection[27923] Controller.m:356 Image loading took: 1.408970 seconds
2006-11-19 02:18:14.508 Reflection[27923] NetworkController.m:214 Service published on port 55015 as "power-mac-g5.local:27923"
2006-11-19 02:18:14.577 Reflection[27923] Controller.m:383 Library backup scheduled for: 03:00 AM
2006-11-19 02:18:17.572 Reflection[27923] EditController.m:595 Spotlight search: '(kMDItemFinderComment == **"CATEGORY*" = "cat38";**cd)'
2006-11-19 02:18:17.744 Reflection[27923] EditController.m:597 18 results
2006-11-19 02:18:27.036 Reflection[27923] EditController.m:402 Setting metadata: {CATEGORY1 = cat39; PUBLIC = YES; }
2006-11-19 02:18:29.683 Reflection[27923] EditController.m:595 Spotlight search: '(kMDItemFinderComment == **"CATEGORY*" = "cat39";**cd)'
2006-11-19 02:18:29.887 Reflection[27923] EditController.m:597 18 results
2006-11-19 02:18:32.251 Reflection[27923] EditController.m:394 running MDImport
2006-11-19 02:19:05.006 Reflection[27923] NetworkController.m:219 Terminating network connection.
Reflection has exited with status 0.


SUCCESS!

Here's where the shit hits the fan.  Immediately, I restart the program.  No other futzing around, whatsoever.  About 4 seconds later:

*1. Open the program.
*2. Do a spotlight search for images for which I JUST I tagged as 'cat39'.  The search SHOULD return 18 results, since it just did 4 seconds ago.  Heck, I used the 4 seconds after I quit the program the first time to make sure the Finder's Spotlight (cmd-space) returned the same results.  IT DID!
*2.5. Now, however, the SAME SEARCH it returns 1 result.  Both in the program and in the Finder.  My program doesn't touch the metadata outside of the Edit method.  This behavior boggles my mind.
*3. I then even run MDImport MANUALLY (through the program AND the terminal).  The Finder get-info window still shows the metadata; but spotlight can't see it.
*4. The search never recovers.  An hour later it's still returning only ONE (1) result of the 18 that should be there.

    
[Session started at 2006-11-19 02:19:06 -0800.]
2006-11-19 02:19:09.460 Reflection[27929] Controller.m:356 Image loading took: 1.311657 seconds
2006-11-19 02:19:09.671 Reflection[27929] NetworkController.m:214 Service published on port 55016 as "power-mac-g5.local:27929"
2006-11-19 02:19:09.737 Reflection[27929] Controller.m:383 Library backup scheduled for: 03:00 AM
2006-11-19 02:19:14.004 Reflection[27929] EditController.m:595 '(kMDItemFinderComment == **"CATEGORY*" = "cat39";**cd)'
2006-11-19 02:19:14.142 Reflection[27929] EditController.m:597 1 results
2006-11-19 02:19:16.251 Reflection[27923] EditController.m:394 running MDImport
2006-11-19 02:19:56.308 Reflection[27929] EditController.m:595 '(kMDItemFinderComment == **"CATEGORY*" = "cat39";**cd)'
2006-11-19 02:19:56.446 Reflection[27929] EditController.m:597 1 results


WHAT. THE. HECK. IS. GOING. ON?

Somebody please put me out of my misery :(

I've tried running:

<code>[NSTask launchedTaskWithLaunchPath:@"/usr/bin/touch" arguments: [NSArray arrayWithObject: [obj path]]];</code>
and

<code>[[NSFileManager defaultManager] changeFileAttributes:[NSDictionary dictionaryWithObject:[NSDate date] forKey:NSFileModificationDate] atPath:[obj path]];</code>

and neither updates the Spotlight data.  Editing the Spotlight data manually however, through the finder, DOES update the index.  Which seems ridiculous to me because I'm using the Finder's AppleScript dictionary to edit the Spotlight data in the first place (on top of running mdimport, touch, and NSFM).

The thing that kills me most is that the search works immediately after the changes, and then stops working later on.  I even set a timer to run MDImport 5 and 20 seconds after an edit (ridiculous, I know), but it STILL DOESN'T UPDATE THE SPOTLIGHT INDEX.

Anybody have any insight?  I need this thing working completely by monday, 11/20/06.  It's all done except for this ridiculous spotlight behavior.
----
What's even more insane is it "works" now, but I didn't "do" anything.  I just `touch` the file about 17 times and spam it with `mdimport -f`, and hey, whatddayaknow.

Very shady stuff; I feel dirty even admitting to the code I just wrote.  I'll report back if I ever figure out what was really happening.

----
You're using AppleScript to use Finder to update the comment field? - that should work just fine... As an aside note that spotlight does not trigger on xattr changes. My only suggestion is a debugging aid, Google for 'fseventer' - it uses the same API low-level API as spotlight to give a visual display of file changes, so if it sees the change then so must spotlight.

----
My suggestion: first change the xattr, then use applescript to tell finder to update the comment field.  No more steps, i.e. do not touch or run mdimport.  As an aside, some depressing food for thought - I've never seen any sort of guarantee (by Apple) that Spotlight will always find stuff, or will find all of it, or will find new changes immediately...

Also, a quick test shows that running applescript:
    
set f to "/Volumes/Work/Installers"
set the p to POSIX file f
tell application "Finder" to set the comment of item p to "test42"

immediately causes a 'content modified' event in fseventer, and appears in a Spotlight search...

