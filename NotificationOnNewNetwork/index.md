---
layout: page
title: NotificationOnNewNetwork
---

I'm trying to understand how to get notifications when my airport detects a new network. I don't want to poll (bad practise, you know). I've looked for a notification, without luck. I have also looked at KernelEventAgent, but I can't find any documentation on how to use it.

Anyone?

What I want to do is a small daemon which checks for a new airport network and if it's the network at my university, it logs in to the network (via a HTTP-call).

--TheoHultberg/Iconara

----

I would point you towards SystemConfiguration.framework, but I don't remember seeing anything in it that would notify you of the mere existence of a new airport network.  However, you can be notified when you **join** a new airport network by requesting notifications on the key "State:/Network/Interface/*<name of interface>*/AirPort" where interface name is something on the line of en0, en1.   You can get the list of interface names by querying the key "State:/Network/Interface" or just use SC's regex support (i.e.     SCDynamicStoreCopyKeyList(dynStore, SCSTR("State:/Network/Interface/.*/AirPort"));) to get the valid keys. -- Bo

PS When I was learning SC, I wrote a test app that (among other things) browses SystemConfiguration keys.   It's pretty hackish, but it helped me get my head around the structure of the SC database.  If you wish, I'd be happy to email it to you.

----

Please mail me your code, I think it would be very helpful. --TheoHulberg/Iconara (theo[AT]iconara.net).

----

Ok, it's sent.  Let me know if you have any questions.  -- Bo

----

Your code was great Bo, thanks for the help. I have now solved that part of the problem, onwards to the next!

I wrapped up some snipplets of your code in a notification center-style class, just register for any of the keys posted by SC, and you get nice notifications when they update.

-- TheoHultberg/Iconara

----

Actually, could you post that code somewhere? It sounds very useful. If you don't have a place to post it, I'd be happy to host it. (And could you send me a copy, regardless, please?) -- Joshua Marker / joshua (at) qcsf.com.

----

You can find the code here: http://www.iconara.net/developer/objectlib/IXSCNotificationCenter.tgz, please send me your changes (if any) and I'll add them, the code is quite basic for now.

Quickstart:

IXSCNotificationCenter *nc = [[IXSCNotificationCenter alloc] init];

[nc addObserver:self selector:@selector(update:) name:@"State:/Network/Global/IPv4"];

It's not a real notification center, it's not a subclass of NSNotificationCenter, nor does it provide all the methods that  NSNotificationCenter do, but it works for now. I was thinking of rewriting it as something that posts notifications in the shared notification center instead, that would probably be more appropriate. I'll post that code when I get around to writing it.

--TheoHultberg/Iconara

----

Never mind the last post, I had some spare time (for once), and hacked a new class: http://www.iconara.net/developer/objectlib/IXSCNotificationManager.tgz, it uses the default notification center. About a tenth of the code...

New example code:
    
systemConfigNotificationManager = [[IXSCNotificationManager alloc] init];
		
[[NSNotificationCenter defaultCenter] addObserver:self 
                                         selector:@selector(update:) 
                                             name:@"State:/Network/Global/IPv4" 
                                           object:systemConfigNotificationManager];


The idea is that the notification manager recieves all the updates to the system config database, and posts them to the default notification center. I associate the manager with the notifications, to make it easier to listen for just system config notifications. Dunno if manager is the right word, but it was the first thing to my mind. Feel free to modify the code.

--TheoHultberg/Iconara

----

When using the IXSCNotificationManager, make sure you're project includes SystemConfiguration.framework or else you will run into Undefined Symbols during linking.  Now that I figured that out, IXSCNotificationManager is working great.  Thanks! JoeCrow

----

to everyone using this: there is a problem that actually doesn't inform you of the changes of that specific IPv4 key if there is no ipv4 address in place on startup of the application that uses this. a fixed/extended version can be seen at http://code.google.com/p/tcmportmapper/ - DominikWagner - the easy fix here - replace the corresponding code in the init method:
    
SCDynamicStoreSetNotificationKeys(
  dynStore, 
  NULL,
  (CFArrayRef) [NSArray arrayWithObject:@".*"]
);


The fixed class can be found at http://code.google.com/p/tcmportmapper/source/browse/trunk/TCMPortMapper/framework/

