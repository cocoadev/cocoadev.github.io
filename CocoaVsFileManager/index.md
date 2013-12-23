---
layout: page
title: CocoaVsFileManager
---

Cocoa's UNIX roots are getting on my nerves.

----

One such problem is Cocoa apps tend to assume an open file's path is constant. This assumption is implicit when using POSIX file paths with one of the many methods which accept NSString arguments as file primitives. This has human interface implications which aren't addressed in the Aqua HI Guidelines. For example the guidelines explain the use of "Save" and "Save As...", but the behavior of such commands will depend on whether the app actually knows where the file is (or is supposed to be?) and if the user isn't aware which behavior is being used could easily end up losing data or getting weird errors.

Apple's decision long ago to deprecate toolbox functions which used file paths was a good one. MacOS users can move or rename files/folders/volumes willy nilly without taking a system-wide census of which files are open by what applications. This is no longer true in Mac OS X. I wonder what would have happened if Cocoa had been ported to MacOS.

Anyway there are some work-arounds for Cocoa developers. There are a couple CoreFoundation methods to convert POSIX file paths to FSRefs and back. Mind you these functions are only available in the closed-source version of CoreFoundation. Cocoa developers can also avoid this problem by using NSDocument exclusively for all file I/O since NSDocument (since OS X 10.1) seems to use an FSRef internally. I don't know if you can move a Cocoa app while it's running without it losing track of it's resources, probably not. Grumble.

It seems to me that Carbon is a good low-level API while Cocoa is a good high-level API which unfortunately uses POSIX low-level APIs. I'm hoping Apple will introduce a lightweight file primitive like FSRef for Foundation and propogate its use to methods which currently use POSIX file paths in the form of NSString's, but I'm doubtful.

There are other issues like iterating directories/hierarchies which the File Manager seems to handle better/quicker (at least on HFS+ volumes). Perhaps a new low-level filesystem API which wasn't invented when UNIX was is in order.

-- PaulBayley

----

comments?

----

Yes, allow me to rise to your flamebait.

I, for one, am quite happy in this path based BSD world we live in. What I see is what I get for the first time on a Macintosh. Problems trying to copy files and applications off of fileservers because they're "in use" are largely a thing of the past. I finally have logical references built into the file system (i.e., symlinks) where they can be traversed consistently using only one function call (with loop detection!) instead of skanky "alias" files interpreted by high-level library functions. I can finally type /usr/include/mach/mach.h (or whatever) and get to my destination in about 2 seconds rather than having to reach for the mouse, wait for the Carbon file manager to stat all of the mounted volumes on my system, watch the dialog, and start clicking through the directories one by one.

On the other hand, I'm no fan of NSFileManager either. I figure FTS can beat both Cocoa and Carbon to an HFS+ race. I hope people get better acquainted with the rich BSD API available underneath these high-level application environments, and stick to using the App-level API for doing App-level things. 

Naturally, this is merely my opinion.

-- MikeTrent

----

I never said anything about aliases.

If I were to say something about aliases I would say they are far more flexible than symlinks since they are an arbitrary description of how to find a file. You can have aliases which break like symlinks and you can resolve them in a fashion which doesn't use a mouse or doesn't search other volumes, so your complaints are unfounded.

----

I think you are being a little cruel. This is just a matter of preference. Mike likes the fact that symlinks are easy to use from a system level viewpoint and you like that aliases are more high-level and easier to use from an end-user viewpoint. I guess it's an inevitable debate over which is better: Ease of use of a Mac or the power of UNIX because face it, the two will never walk *perfectly* in stride.

- Sam

----

Pointing out that the discussion wasn't about aliases, I'll also repeat myself yet again by saying aliases can behave *exactly* like symlinks. The only problem is whether UNIX can adapt to using something other than symlinks proper. 

There is nothing powerfulbout symlinks, rather the opposite.

-- PaulBayley

----

My complaints that aliases are skanky because they aren't a filesystem feature are unfounded because I can resolve them w/o a mouse?!? And we aren't talking about aliases in a paths vs FSRef discussion?!? Normally I expect better formal reasoning in flame bait. But I'm still willing to play along (although I agree w/ Sam's notes above) assuming you are able to clarify...

I look forward to your next response.

(Also ... it's fairly rude to ask for comments and then claim soneone's observation is "unfounded". If you want a soapbox, that's fine, but don't pretend to solicit comments)

-- MikeTrent


----

1) The reason your complaints are unfounded is because your complaints about aliases have nothing to do with aliases but rather the inability for UNIX legacy stuff to resolve them. The fact remains that you do not need a mouse to resolve aliases, you can easily write a command-line program to resolve an alias without a mouse. You can also create aliases which behave (break) just like symlinks. If you want I could say the same thing with different wording a third time.

Your observation is unfounded. All you're observing is how the Finder resolves aliases and the inability of legacy UNIX stuff to do so, not the 'skankyness' of aliases.

2) You may talk about whatever you want, but I did not bring up aliases nor did I imply aliases. The issue I brought up was the crappy way in which Cocoa apps refer to files. This isn't flaim bait, the proof of which is you're getting pissed about an issue I didn't bring up.

-- PaulBayley

----

Ok, I see the confusion now. 

I'm claiming "File Manager" is lame because it introduces filesystem-like features at a library level, rather than leaving these features in the filesystem where they belong. Aliases tie in here as an example of this problem. I'm claiming aliases aren't a filesystem feature and are thus "skanky". Because they're not part of the filesystem they must be interpreted correctly by the application. This means, and I'll say this carefully, carbon apps using "File Manager" have to do additional work to support aliases. This is the heart of why I believe alaises are skanky; and this represents what's wrong with the "File Manager" API. These kinds of features only work if everyone is playing by the same rules. Is the idea of providing evidence to support a claim unfounded?

Since you also missed this point I'll say it again: I'm not claiming Cocoa's APIs are better than Carbon's APIs. I dislike NSFileManager and File Manager both. I strongly advocate the POSIX API layer and hope both Cocoa and Carbon users are able to take advantage of its rich functionality. Specifically, I disagree with your claim that Carbon is a good "low-level" API. I think Carbon is a terrible low-level API (which is why we now have Mac OS X) and, like Cocoa, should be treated like a high-level application library. I also disagree with your claim that the Carbon API can iterate through a filesystem faster than POSIX API (which I assume is what you mean by "a filesystem API which wasn't invented when [sic] UNIX"). 

You also misunderstand my emotional involvement. I'm not "pissed off", I'm amused. It's a lot of fun going through flamebait issues like this, so long as everyone involved follows some basic rules: no name-calling, . Occasionally some new insight develops that help give you a better perspective on things.

And, yes, this is so totally flamebait it's burning a hole through my web browser. You can go dig through years of flameage on this topic on Omni Group's developer mailing list (www.omnigroup.com) if you like. This is not a new discussion.

-- MikeTrent

----

An alias is no more or less part of a filesystem than a symlink. A symlink is just a file with a POSIX file path with a flag signifying it is a link. BOTH have to be interpreted, the only difference is the API used to do so. The suggestion that you need to use extra code in Carbon apps in order to play nice with aliases is false, that is only true with Cocoa apps and only because it uses UNIX APIs which is severely limiting. Yes, your claim is unfounded...yet again.

The very idea that the POSIX API is "rich" is, if you will, "rich". If it were so capable I wouldn't have to point out why paths are so limiting. There is no equivalent of the filesystem reference in POSIX and I doubt there ever will be. That is a fault of POSIX. Quite frankly we should be moving away from filesystems altogether.

As for being flaimbait, I didn't bring up aliases, you did. The issue I brought up was FSRefs vs NSStrings, an issue you have decided to avoid in order to discuss your pet peeve.

-- PaulBayley

----

"An alias is no more or less part of a filesystem than a symlink"

That is simply not true. Symlinks are filesystem features, aliases are files that are interpreted by high-level application functions. If you still disbelieve check out the Darwin source code.

"The very idea that the POSIX API is "rich" is, if you will, "rich"."

You are welcome to disagree with me, just as I disagree with you. I do agree we should be moving away from filesystems entirely, which makes this entire path vs FSSpec discussion irrelevent. 

"As for being flaimbait, I didn't bring up aliases, you did"

Why are you still harping on aliases? Have I not clarified this? The flamebait issue here is paths vs "filesystem references" (to quote from you above). The line of text that kicks off the entire flamebait-ish nature of this conversation is "Cocoa's UNIX roots are getting on my nerves" right at the top of the page. You might as well say "GUI is better than CLI" or "Windows is better than Mac" or any other classic flamebait topic.
----

It's funny, I've met a few mac developers who kind of migrated over to OS X, shareware folks who were pretty happy (somehow) with the old API's and ways of doing things. They all seem to share this sentiment. I find it surprising, considering the somewhat whimsical design of carbon (the origanization of it seems poor to me).

This sentiment comes up a lot. Poor Apple. They just can't win. 

I will say, just to toss fuel on the fire, that the POSIX API's are some of the most well thought out and sane to date. My Only Complaint(tm) is that many of the calls aren't thread safe. 

-- DaveFayram

----


Perhaps we should take a user poll: Is the "CocoaVsFileManager" topic flamebait?

YES: 9 (from the limited sample here -- KritTer) 
NO: 3

-- MikeTrent

----

I'm pretty sure that symbolic links are NOT part of the file system... Hard links are, I think, or at least they are in theory but I think I remember reading they aren't supported by HFS+ and therefore have to be emulated at the FS layer. symlinks are resolved at a lower level in the APIs, though. Of course, I could be wrong.

-- FinlayDobbie

----

In Mac OS X symbolic links ARE part of the HFS+ and UFS filesystems. Hardlinks are as well, although some effort has been spent making the HFS+ filesystem emulate UFS's behavior. Remember emulating behavior doesn't mean you don't support a feature. Files claiming to be symlinks within the filesystem (on disk) are resolved (or not) within the filesystem code (in kernel). I don't see how that qualifies as emulation.

But if you're going to be unreasonably technical, you could claim that directories, symlinks, devices, sockets, etc. are NOT part of traditional UNIX filesystems. Everything is a file with 1 or more link entries (i.e., non-hardlinked files are a special case where there is only one link entry for each inode), all other behaviors are emulated.

-- MikeTrent

----

File management with Cocoa is a little bit of a hassle. Using paths is quick and easy, but can become a problem when the file is not guaranteed to not be changed by another application. There are countless scenarios of which this can happen, for example: A beginner user opens a file up, edits it and decides that they want to change the name of the document. Before saving and closing, they change the name in the finder and then save. If the application is keeping track of that file via a path, then you're going to run into trouble. Either (a) it will crash, or (b) you'll end up with duplicate files, one being old the other new - and being named in reverse. Even worse the beginner not realising will delete what they think is the old file and loose their recent changes.

Using FSRef is a much more reliable way to handle files, as regardless to whatever happens to the file during operation the application can still keep track of it. Unless of course it is deleted. The trouble is that Cocoa doesn't have a good wrapper for FSRef, but at the same time it isn't all that hard to set up one for your app. I have created one and put it in my personal development framework of which I add to every Xcode project. File management for every app I build is a snap and very easy.

However, cocoa still requires a better file manager class, and also workspace class. Both are hopelessly out of date. Especially seeing as Carbon is not getting 64 bit support...
----
Where is this mythical "beginner user" who uses the file browser to rename files that are open in an editor ?  I suspect that nearly every user has used Windows or Linux before using Mac OS X, and therefore has none of the expectations described above.

----
No "beginner user."  I do it all the time.  I'm often working on something in TextMate and realize that I need to move files around.  I often make a script files on the Desktop as a temporary location and then move them to a more permanent location when I'm happy with it.  Of course, I often find out that I need to make a few more changes afterwards.  I'm often a bit disappointed that TextMate doesn't recognize the file move so changes to the open script file makes a new file on the Desktop.  I have to close that editor and open the new file.  FSRef-based editors don't have that problem.  It's not a mythical problem, it's a practical problem. -G

