---
layout: page
title: ILifeMediaBrowser
---

**NOTE:** This page is now obsolete.  See IMediaBrowser for an implementation of the iLife Media Browser.

Does anyone have a good idea of how I can implement a Keynote-style media browser that shows iTunes songs, iPhoto photos, and the contents of the Movies folder in the user's home directory? I can't seem to find anything on the Internet that does this and is open source. I know that Keynote has the NSPopUpButton in a Nib and then a single custom view beneath it for the playlist/album list and then the browser. Thanks. --LoganCollins

I, too, have wanted this. Whatever info someone would have would be awesome. -- JasonTerhorst http://goo.gl/OeSCu

----
It's not too hard ...


***Step 1** - Read the iTunes and iPhoto XML libraries into their own individual NSArrayControllers & set up their table views. ***Step 2** - Read in the contents of the Movies folder into its own NSArrayController and set up its table view.
***Step 3** - Use custom NSButton images and plagiarize, plagiarize, plagiarize Apple's buttons (or, for a novel idea, create your own) to create your own 'toolbar' for your palette.
***Step 4** - Profit! 


----

Solved! I just hacked their private framework. It all works just as in Keynote but for two caveats: 1) The little icons for iPhoto, Movies, and iTunes don't show up in the popup button because this is a quick hack; I leave it as an exercise to the reader; 2) Movies don't display their thumbnails, just a QT movie icon. I tried to load the movie files and get the first frame from each but couldn't really get that to work, so I just left the code commented. Maybe someone here can fix that up.

Download: http://www.andymatuschak.org/files/MediaBrowserTest.zip (you'll have to copy MediaBrowser.framework to ~/Library/Frameworks or put it inside of the app bundle for it to work)

Screenshots:
http://www.andymatuschak.org/files/MB1.jpg http://www.andymatuschak.org/files/MB2.jpg

-- AndyMatuschak

----

Whoa! Awesome! Several tips of the glass to you, Andy! ... The only thing is.... will Apple change those frameworks, considering that they are private, and thus leave us hanging if we use them?

Oh, I tried the example, but got the message:

"[Session started at 2005-07-29 11:41:08 -0500.]
dyld: Library not loaded: @executable_path/../Frameworks/MediaBrowser.framework/Versions/A/MediaBrowser
  Referenced from: /Users/jason/Desktop/MediaBrowserTest/build/Debug/MediaBrowserTest.app/Contents/MacOS/MediaBrowserTest
  Reason: image not found

MediaBrowserTest has exited due to signal 5 (SIGTRAP)."

What's up with that?

-- JasonTerhorst

----

Oh, probably just a configuration error. I think I forgot to have the framework copied into the executable. So add that build phase or just copy MediaBrowser.framework into ~/Library/Frameworks. Sorry about that.

And yes, Apple may change those frameworks. That's the risk that comes with using them, unfortunately. But if they did, it would probably be trivial to get this thing working again. Oh, and if you were to distribute MediaBrowser.framework in your app directly (which is actually what every app that uses MediaBrowser.framework does), you would have impunity from their changes. That miiiight be illegal, but so long as your app is free, I doubt they'll care. And if your app isn't free, well... it should be! And open source!

-- AndyMatuschak

----

Yeah, I just figured out why it wasn't working - had to copy framework to the folder. It was pretty awesome to play with, and it's amazing that was so quick and easy for you to do!

----

Now, after playing with it a little bit, I'm having a minor issue with it.... I try to click on the panel or the menu, and nothing happens. It's as if it locked up, yet it didn't, because there is no spinning beachball. Anyone else have that problem, or a possible solution? I even re-downloaded, still having trouble.

----

I'm afraid I haven't seen that problem exhibited myself. Perhaps some property of the panel isn't getting set the way it should. It was just a quick hack. ^_^;; Is this happening on the unadultered example?

-- AndyMatuschak

----

It seems to. I did get to try it a couple times, and then, out of nowhere, it started the odd behavior. I've redownloaded it a couple times, and I moved the framework around to the various framework folders, but nothing has changed. Is there a setting I could mess with? What version of XCode was it created with? Even with the fresh copy of the framework, it still has problems. --JasonTerhorst

----

I used Xcode 2.2. But I doubt that's why. Hm. **<-- In the future lets only post examples created and updated by versions of Xcode that are final release, not still in developer testing.**

-- AndyMatuschak

----

I think he meant Xcode 2.1. Apple changes versions quite often, so confusion is inevitable. --JasonTerhorst

----

Ah, no, actually I am using Xcode 2.2, sorry; I didn't think that it might have caused issues. But I really doubt that caused the problem at hand. -- AndyMatuschak

----

Do you have access to a machine where you could test the project, one with an earlier, non-beta version, just humoring me? Or maybe finding a way to use a non-beta 2.1? I've gotten emails from people before about my apps, and the problems are always caused by the weirdest things, so I'm really curious as to what could be doing this. Thanks. --JasonTerhorst

Well, I ran it again, with some debug breakpoints. All I've gotten so far is that it successfully loads the song list, and then freezes. So, I think I've narrowed it to the framework, or to whatever Apple frameworks are called, but that probably doesn't help too much right now. If you can tell me what I should poke around at, I can maybe narrow it down much more.

**I haven't seen the Standard Warning here yet so I'll go ahead and state it: Using private frameworks in production applications is a BAD BAD THING that could leave your (possibly) PAYING CUSTOMERS without a feature they might rely heavily upon. Apple is free to (and has most definitely in the past) change any private, undocumented framework at any time. The info on this page certainly is neat but it's just plain dumb to rely on it for a shipping application. Be warned.**

Also, embedding the framework in your application and distributing it is a copyright violation. If you do this thing (which you should not ever do), then you must make sure to link against the framework which is already on the computer, rather than bundling it.

----

Of course, it'd be difficult to dynamically link against the framework, given that it isn't installed in ~/Library/Frameworks or the like�only in the bundles of certain applications. But yes, you probably shouldn't use this. I just did it because I enjoy class-dumping things. :) -- AndyMatuschak

*Then don't do it (distributing it) at all because you'd be breaking the law with absolutely no defense against a heavy penalty. We all know Apple will (and has every right to) pursue.*

----

Well, does anyone else have a possible solution for this? Cocoa developers would want to be able to work with iTunes, iPhoto, iMovie, and others. Is there a way to do Andy's hack, minus the private frameworks?

----

Of course there is. I originally posted an explanation (see this page's early history) but someone removed it (so I'm not going to even bother posting it again). It involves parsing the xml files (reading them into a dictionary) for iTunes and iPhoto (then just doing a flat directory listing of the movies folder) and displaying them in your own panel. Really, folks, this isn't too hard. If you have specific questions about the specific steps, post them clearly and concisely and I or someone else will point you in the right direction - this is as good a place as any to provide some links (like some other app-specific pages in recent history should be) about 'how to do it like they do it' for the media browser.

----

Yes, it is in there, but it may have been removed because it seems to come out as sort of bitter or rude. That may not have been your intention, but that's what some people may have thought. Then, Andy came out with a pretty impressive hack (and in a very speedy manner), so your comment was probably pushed out. CocoaDev seems to have a lot of people lately saying "look it up yourself!!", which is rather mean. This could explain why people are complaining about this site being like slashdot lately.

*See HowToWriteHelpfulAnswers...*

----

For a change from the bickering, why dont some of us gang up and create an open source version of the media browser? Surely if we all work together we can develop a version that will be just as fast (at things like scrolling through photo thumbnails) as Apple's, plus, it wouldn't be private, and would greatly help the Cocoa development community.

-- Super Freak

That is an awesome idea. Apple wants us to make the iLife apps even cooler, so this would be something that would help the Cocoa development community.

That's an excellent idea. I'd love to help out if you're still working on it after I finally get Pixen 3 launched. Sorry for inciting bickering around here. -- AndyMatuschak

Browsing of RendezVous/BonJour shared playlists and album would be very nice too.. :-)
-- StephaneBoisson

The RendezVous (or whatever they call it now) browsing sounds cool. One question on the side, though - can commercial developers still have the ability to use this in their products? Is there a licence to do so? That would benefit everyone, since Apple is quite mum about the iLife media browser, so commercial projects can use it.

*iTunes and iPhoto's libraries are both saved in extra XML files specifically for developers to use . Building your own 'media browser' is perfectly fine.*

**Is this written down somewhere?  I've read it before on CocoaDev, but nowhere else.**

----

Apple does sometimes change the way the library for iTunes works - for instance, podcasts are now in the folders, and they've moved the old xml listing to a "Previous iTunes Libraries" folder, and created a new one which I believe would contain the podcasts lists. There are other functions that we would also want to incorporate, like the iMovie projects. If we put our heads together for an OSS project, we could find cool ways to do this.

Is there an XML file for iMovie, or do we need to just poke at the Movies folder and hope for the best? Another thing I wondered, how does the Media Browser so quickly show thumbnails? Considering that it would have to look at each of those files, shrink them down, and display the shrinked version, I'm surprised it's as fast as it is. Any ideas? Pre-caching somehow?

I've also noticed that sourceforge doesn't have any iLife Media Browser projects, so this would be very much welcomed.

*I'd be more than happy to contribute, someone want to set up SVN/SourceForge?*

I'll do it if no one else does by tommorrow morning. I need to get to bed. What kind of name would everyone have in mind for this? Any creative, catchy names? By the way, I'd recommend SourceForge for this particular thing, since I'd guess we want to get this out there for people to see, right?

I might be able to contributed a little.  My name vote goes for the simple and to the point "MediaBrowser". Or maybe "CDMediaBrowser" because it will be a CocoaDev community project.
--ZacWhite

CDMediaBrowser will make people think that it is exclusively for browsing Compact Disc's and related media. Honestly, I don't see why we couldn't use iLife Media Browser. People use Apple product names in their (commercial) software all the time, so for an open source project there shouldn't be any problem.

-- Super Freak

What about alluding to the iLife suite by just calling it "iBrowser" or "iMediaBrowser". We don't have to actually put iLife in there.
--ZacWhite

I think "iMediaBrowser" would work great. "iBrowser" is a bit too much like a web browser.

-- Super Freak

----

Since no one seems to have registered it yet on sourceforge, I went ahead, but I'm stuck on the decision of which license to use. What license can we use that will protect us and prevent someone from trying to pass it off as their work, yet allow commercial developers to use it in their program without having to put up the source of their work. Also, any designers' works must be protected completely. The developers that use this material in their program would be required to simply redesign the parts of the interface that are unique, and/or give credit where credit is due, but not have to publish their source code. What license on SourceForge best fits this idea? There is a rediculous list of choices, none of which are very short (though very complete), and it is my understanding that the GPL isn't one that fits the free for commercial use idea, so we'll need to dig through the list. If anyone sees a good license, post it here...

... ah! On further digging, it seems one of the easiest answers is the best one. BSD license everyone? Post your ideas with the vote below.

----
How about a vote? Just add something if you want.

Name:

  iMediaBrowser (3) *Looks like this name is available. Google returned nothing.*

  Bob (1) *Oh, wait, Microsoft already took that (for those who weren't in grade school in the 90's). :-)*

-- Oh, please don't remind me of that painful thing. Ironically, Gates married the woman who was behind the idea of that project.

License:

  BSD (3)

I put my vote for BSD, but I actually prefer the MIT license.  Same meaning, better wording, in my opinion. But as the two are conceptually equivalent, I figured I wouldn't over complicate the poll by adding an extra option. --OwenAnderson; I agree wholeheartedly. --AndyMatuschak

----

How long do you want me to wait before posting to SourceForge? ... or would someone else like to do it?

I'm going to go ahead and declare this a landslide victory for iMediaBrowser under the BSD license. I mean, look at those numbers! Go ahead and register it on sourceforge. Although...I would prefer svn but I doubt I would be the one who works on the project the most.

I prefer SVN as well, and am willing to donate hosting at silentcentre.net if we need SVN hosting. And actually, it looks like it's a landslide (2/3) for MIT (also called the old X11 license, http://www.x.org/Downloads_terms.html ). ;-) --OwenAnderson

----

Well, I registered the project (though it's still pending). If you'd like SVN, please post back here if you can set one up for us. At the very least, we could use the SourceForge as a place to host the website and downloadable bits for people to see (since SourceForge has and can handle huge amounts of traffic). We can decide later what we want to do for other things.

----

Someone asked earlier: *Another thing I wondered, how does the Media Browser so quickly show thumbnails? Considering that it would have to look at each of those files, shrink them down, and display the shrinked version, I'm surprised it's as fast as it is. Any ideas? Pre-caching somehow?*

Well, for movies, iDVD apparently reads in all the files in the movies folder and creates a thumbnail from the poster frame. This is actually very simple (especially after the release of QTKit in QuickTime 7.0). It then saves the thumbnail images into the users caches directory under com.apple.idvd. If you dig in there, you'll find a Thumbnails directory which contains jpg thumbnails and an XML file called TNSCacheDB. Inside this database file, the movie files are referenced to the thumbnail images. It appears that the media browser checks for filesystem updates in the movies directory to update it's contents, as it updates instantly when a compatible movie is added to the directory.

Now for photos, I think that it simply loads up iPhoto's database, and reads in it's thumbnail contents. As for the scrolling performance, but I'm willing to bet that it benefits greatly for iPhoto's heavily optimized code.

I cant seem to find where Keynote puts it cache, but since the window and thumbnails are resizable, so I don't think that it caches.

-- Super Freak

I have thrown together some code for getting the thumbnail from an iMovie project. This is just a very quick project with no error checking, and very little code. The document path must include the .iMovieProject file extension to work. Download it at http://www.nancesoftware.com/iMovie_Thumbnail_Retriever.zip (39.5 kb). It requires Xcode 2.0 and QuickTime 7.0.

-- Super Freak

Ok, the SF page is up (http://sourceforge.net/projects/imediabrowser/). Lets move this discussion to IMediaBrowser on this wiki.
--ZacWhite

----

Super Freak, does your code require a newer version of iMovie? I think my version is iLife 04, and I can't seem to get your project to create a thumbnail from the iMovieProj file that I have. Are there any other requirements we should know about? Thanks for the awesome example. -- JasonTerhorst

*iMovie 5 has a much different file format than any previous version of the program. Previously, iMovie created a folder for the movie, which contained all of the information, including the project file and the media file. The newer iMovie creates a bundle (signified by "iMovieProj**ect**" instead of "iMovieProj") with everything in it. --JediKnil*

Err... yea .iMovieProject my bad. I'll have to fire up the Panther system and work out a solution for iMovie 4. I wasn't event thinking about previous versions when I through together that code.

-- Jacob Hazelgrove

It's good that Apple finally tied the goofy folder thing into a single bundle file, but our media browser would definitely need to be able to read the projects from the last couple of iMovie versions (just in case, right?). -- JasonTerhorst

*I don't know about that...you have to dig into the bundle just to open the movie in Quicktime. --JediKnil*

I was just referring to the fact that folks I know would drag the file out of the folder, rather than dragging the whole thing and burning on a CD, at least back in the OS 9 days, so this will make them understand that they need that folder (and everything inside it), in order to move their project to another machine. Being a single file makes it easier for people to understand - just drag that one thing to a CD, click "burn" and it's done. *ding!* -- JasonTerhorst

----

Well, I took the liberty of updating his code for the thumbnail previewer. You can drag and drop a project's file from iMovie 4 or later into the little box (in addition to the path field), and it will show a thumbnail. It detects which version the file is, and works with that. http://www.jterhorst.com/iMovieThumbnailRetriever.zip -- JasonTerhorst

I've put up something fairly significant, but since I'm packing to go away for the weekend, I don't have the time to mess with Sourceforge at the moment. To download what I've done so far, go to http://www.jterhorst.com/iMediaBrowse.zip. If someone can figure out the sigbus problem for me related to the iTunes XML dictionary, that would be awesome. I commented out the problem code in the meantime, so you can see that it's able to find all of the pictures and movies in the user's respective folders, but iTunes just has that sigbus issue. See what you think, and drop me an email! -- JasonTerhorst

Sept 11, 2005 - Fixed issues, now adding in previewer and other features. Note that until everything functionality-wise is working, we'll just keep the ugly UI. Once everything is turning perfectly, we'll focus on making it look as much like the Apple version as possible. -- JasonTerhorst
 Pour participer  Orange et garder votre  numéro, vous aurez  peut avoir   comptes   propriétaire  ( Règle) [http://obtenir-rio.info numero rio]. Vous obtiendrez  pour  aucun coût par appelant   mots  du serveur ou du service à la clientèle  support clients   du   fournisseur  [http://obtenir-rio.info/rio-bouygues numero rio bouygues] . Vous ne  certainement  acquérir  un SMS  avec vos . Avec  votre propre  [http://obtenir-rio.info/rio-orange code rio orange], alors  vous êtes capable d'  vers le  offre de  de votre choix respectifs ON  orange orange .

