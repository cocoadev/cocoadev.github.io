---
layout: page
title: FlushAppEventQueue
---

I'm working on a (quick-n-dirty) app which makes heavy use of NSTask to run command line operations in the background. The operations are batched, so I keep a progressbar updated so I can tell what the level of completion is.

Here's an example:

    
int i, total = [found count];
for( i = 0; i < total; i++)
{
	[progressBarTF setStringValue: found objectAtIndex: i]stringByAbbreviatingWithTildeInPath;
	[progressBar setDoubleValue: (double) (i + 1) / (double) total ];
			
	//the MHProjectFile class makes heavy use of `pbxbuild -list` and `pbxutil` to parse project info
	MHProjectFile *pf = [[MHProjectFile alloc] 
			initWithProjectFile: [found objectAtIndex: i] 
			andFolderScanner: archiveScanner ];
			
	[projectFiles addObject: pf];
}


Now, since I've got the progressbar set to be threaded, it does draw and update and by and large, it seems to work pretty well. But, the app itself is completely unresponsive while this is running. It's annoying because this will cause stuff like sheets to freeze half-closed (until the batch is 100% complete) and other annoyances.

Now, were this meant to be a serious app for general use, I'd either make the NSTask processing run in a seperate thread or I'd let it use the NSTaskDidTerminateNotification notification for asynchronous behaviour. However, this is just a quicky program (it's an automated project backup utility -- it scans my projects folder and backs things up with timestamps, it was inspired by a shell script I saw posted here a while back) and as such that all seems too complicated. This was meant to be an afternoon work -- now it's two afternoons. I'd like for it to stay that way ;)

Now, since I used to be a Qt programmer on linux, my immediate thought was, "just flush the app event queue after each chunk". See: http://doc.trolltech.com/3.2/qapplication.html#processEvents

But I can't seem to find anything like that for Cocoa. Is there such a beast? It would be awfully convenient.

--ShamylZakariya

You have two options:

1) Run your algorithm in a separate thread. I.e., when you start a backup, fork off a new thread. Run your process and update the progress bar from that other thread. This can lead to other errors if you're not careful, and I would recommend option 2:

2) Run your NSTask asynchronously, as noted above. Should take you about 2 minutes to change your code appropriately, which still qualifies as "quickie" in my book.

As for  "just flush the app event queue after each chunk" ... Mac OS 9 is dead. Polling is a thing of the past. I encourage you to learn and master other techniques, such as notifications and messaging. If, for some reason, you absolutely must poll, you can get this to work as a 3rd option:

3) Enter a modal context and continuously poll for events with a very small timeout. This is lame because it uses CPU unnecessarily, steals time from other processes, drains your powerbook battery, and other unfortunate events. I don't recommend it.

-- MikeTrent

I respect your emphasis on avoiding old fashioned programming models, but for what it's worth the fact that they're old doesn't mean they're *bad*. Qt is a remarkably modern API full of polymorphic goodness and all sorts of runtime introspection ( albeit, having come to cocoa, I don't think I'll ever go back ) -- trolltech provides stuff like forced flushing of event queues to accommodate easier porting of legacy code. It's a good thing, even if we shouldn't, generally, make use of it.

But anyway, I'll probably take option number 2, even though I'm absolutely comfortable with multi-threaded programming using the pthreads c interface. Well, anyway, this program really is a quickie, and not meant for general use, so I think I'll just go option 2 and let it be. I've got more important work to get back to!

--ShamylZakariya

No, stealing CPU time and draining my battery life means they're *bad*. Avoid it as much as you can. Mind you, in this modern Internet age, not polling can be impossible (consider POP and IMAP), and the cost of polling is even greater (network/modem bandwidth, server CPU time). So I admit sometimes you have no choice.

Blocking is also old-fashioned, and I think it's fine. For that matter, memory protected symmetric multiprocessing isn't exactly "cutting edge" technology either, and it's just fine too. Age isn't the issue. I find many Mac OS 9 programmers are used to polling (there was no alternative) and they bring those bad habits with them into Mac OS X. When you do have a choice to poll or not, I believe not-polling is always the right choice.

-- MikeTrent

I'm not much of a fan of wasting my powerbook's battery either. But, unfortunately, short of writing a custom parser for pbproj bundles, I intend to keep using pbxutil and pbxbuild to get the work done; as such, running those to get info on all the projects in my ~/Projects folder takes about 30 seconds at 100% cpu. As far as I know, my program is using only enough to keep the progressbar animated; pbxbuild, pbxutil, md5 and tar are doing all the *work*.

It'll be interesting to see how to go about using the task ending notification to bring asynchronicity into the mix; but the honest truth is that that can wait until I figure out why `pbxbuild clean` is halting (locking and using 100% CPU) on two of my projects (but not the rest).

--ShamylZakariya

