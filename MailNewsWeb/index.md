---
layout: page
title: MailNewsWeb
---



I'm starting a project that will involve a rather unusual client interface to email, newsgroups, and retrieving and posting webpages. I'm deciding on a platform, and OS X looks about ideal, though my experience so far is on Windows. I've done socket communications before, but would rather not - this project will be hairy enough without reinventing the wheel.

So...is there a nice easy API available for mail, ftp, http, and nntp? My first thought was that Cocoa would have something, but I couldn't find anything in Apple's docs. My next thought was that I could wrap Unix command-line stuff, and I found a tutorial (through this site) for doing that. Is this my best option?
----
Just curious ... who is "I'm"? Since Wiki doesn't have users and thus can't log user information, it really helps to tell us who you are so we have some context.

-- MikeTrent
----
Thanks for the tip, Mike, this was my first wiki post. I'll write up a little bio when I get a chance.
--Dennis
----
Take a look at the OmniFrameworks, especially OmniNetworking.

-- JensBaumeister
----
I did look at that page, didn't see any mention that http and so on were built in. Is that the case? Also, I forgot to mention that I need something GPL-compatible (unless it's part of the OS). --Dennis

----

OmniNetworking itself is a socket layer, but there are other OmniFrameworks that sit on top of it to provide http, and other services.  Check out the link to their site for more specifics.  Basically, it's most of the guts of OmniWeb.  I found everything above OmniNetworking though to be difficult to follow, especially for Cocoa newbies.  The Omni folks have years of experience and it shows.  ;)

-- StevenFrank

----

Hasn't our illustrious host created a basic socket class? There's a link somewhere on this site

----

Yes, I wrote SmallSockets.  But it too is just a socket layer, it doesn't implement HTTP or anything fancy like that.

-- StevenFrank

----

I think TheOmniGroup has OmniWebFramework (OWF) or something, which can do HTTP getting, and possibly FTP too?

-- FinlayDobbie

----

Yep.  It's kind of complex though.  See OmniFrameworks for more info.

-- StevenFrank
----
Hmm. So far it sounds like Omni is the only thing available, and it's hard to work with--plus when I looked at the Omni license, it prohibited distribution of modified sourcecode. Probably I'd be better off using Unix stuff for networking.

It really seems to me that common internet protocols ought to be part of standard OS APIs at this point. Windows does have some of this - it's fairly simple to do http retrieval, mail, or ftp from a VB program. 

--Dennis

----

Why would you want to modify the source? It works fine.

-- FinlayDobbie
----
For one thing, I'm planning on a GPL release, which means I need code compatible with the GPL. For another, given that this will be open code, if the interface to Omni is hard to work with that could be something worth fixing, to make it easier for anyone who wants to contribute to my project. For another, any protocols (present or future) not in Omni might require changes to Omni code, so the protocols can be added in a way that's consistent with the others.

I just picked up LearningCocoa, and it mentions in passing some built-in internet protocols. I may end up just picking up an iMac and seeing for myself.

--Dennis

