---
layout: page
title: UsingRendezvous
---

Has anyone used Apple's new Rendezvous technology in Cocoa?  I'm trying to figure the best way to get started to add the capability to a game I have been writing.  Ideally communicating on a peer-to-peer basis, perhaps with one of the players acting as a server for the others who respond as clients.  The end goal being able to play head-to-head over the internet.  (Initially tested on a local LAN)  Rendezvous seems to be the best method as it can be added to a Windows version should I decide it makes sense.

----
Rendezvous is just resource location.  You'll still need to design / adopt some client server architecture appropriate for your game.

----

Check out the General/RendezVous page. It contains a lot of articles on working with Rendezvous. I found this one to be particularly useful:

http://www.macdevcenter.com/pub/a/mac/2002/11/08/cocoa.html

-- General/RyanBates

----

Thanks for the link.  I'm not worried about the TCP/IP communication issues as I'm quite familiar with socket programmings from my C++ work with Windows.  I just wanted to design an architecture that didn't require a dedicated server running 24/7 to manage the connections for people to find each other.  Rendezvous appeared that it might fit the bill.

----

For a LAN, Rendezvous would be perfect.  If you're wanting to have it work for two arbitrary machines on the Big Bad Internet, then you'll probably need the 24/7 dedicated server.   For Rendezvous to work beyond General/LANs requires some special routing stuff I don't understand to be set up.
