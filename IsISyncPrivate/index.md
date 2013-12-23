---
layout: page
title: IsISyncPrivate
---

It occurred to me recently that I might want to hammer out a quick utility to sync specific folders on my laptop against my iPod, with my iPod acting as a backup, but should I modify them on another machine ( my work machine ) they'd then overwrite the same files on my laptop. Basic stuff. 

So, I thought, iSync's obviously the place to go. It "knows" when my iPod is inserted. Presumably it has timestamping functions and all that hoo-ha. But, as far as I can tell, there's no developer documentation. I searched apple's docs, I went into the KB, so on and so forth. WTF?

So, I ask -- is there developer documentation? And, if there isn't, why not? Seriously... why not. How could it possibly hurt apple to widen the third party market for PDA type devices on their platform?

I have to cool down, I sense a rant about Apple coming on...

--ShamylZakariya

To answer the one question I can *is there developer documentation?*

No. See the following link - [http://cocoa.mamasam.com/MACOSXDEV/2004/01/1/81551.php]. iSync does use SyncML, which is a standard, so you may be able to work something out with that. [http://cocoa.mamasam.com/MACOSXDEV/2004/01/2/81639.php]

The reason that iSync isn't open is the same reason that AddressBook, for instance, wasn't open until 10.2 despite it existing for some long time before then.  The reason is that they completely changed the API between 10.1 and 10.2 - pre 10.2 it was under development.  iSync is in a very similar situation (ask me how I know!).

Hope this helps.

----

I'll bite. "How do you know?" --ShamylZakariya

Heh - that was sort of a glib/rhetorical remark.  Let's just say that I've had to work with both the AB pre-10.2 and iSync currently.  There are some similarities between the two, and some functionality in iSync itself for versioning of plugins.  So the writing on the wall is that they either intend to change it or were worried about changing it, and so they left that door open.

----

OK, that's reasonable. I'm not happy with it -- I'd rather see framework versioning be used with the intent of getting something out into the hands of developers so it can be tested in the real world, but hey... 

I'd like to know, then, if there's a procedural way to attach folder actions. Perhaps a command line tool? The only way I know is via the dialogs in the finder. What I guess I might have to do, to get around the lack of iSync, is attach a folder action to /Volumes to be called when a new device is attached ( I know NSWorkspace can detect the insertion of new volumes but I'd rather not have a program have to be running 24/7 just to get notification ) I can then have the applescript run a helper app to detect if the mounted volume is holding sync'd folder and so on and so forth. Does that sound like a reasonable approach? 

--ShamylZakariya

----

You mean you'd rather not have *another* program running to get notification ;) Folder actions are handled by a process launched at login - I think SystemUIServer.

There's an Applescript way to do it - check the System Events dictionary:

    
attach action to: Attach an action to a folder
	attach action to  folder  -- The folder to which the action is to be attached.
		using  Unicode text  -- a file containing the script to attach
	Result:   folder action  -- the reply for the command


hmmm... maybe this site could use a BEGINAPPLESCRIPTCODE style?

