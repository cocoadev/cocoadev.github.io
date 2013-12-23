---
layout: page
title: HelpWithDistributedObjects
---

Hey all!

I've been trying to get distributed objects working with NSSocketPort and NSConnection and stuff.. but I just can't do it!

I've been looking at an example from the maker of iConquer and at a tutorial over at cocoadevcentral.com but it just won't work!

Is there anyone who can help me out here, or maybe make a server-client example?

Huge thanks in advance!


  Regards,
    AntonKiland.

---

Can you add the URIs for your examples?

----

check out NSConnection --zootbobbalu
----
try http://www.gnustep.it/nicola/Tutorials/DistributedObjects/index.html

----
This was brought up in IsNetworkingAsHardAsItSeems and I see you have posted to that page (and I assume read through the information).  Is there any specific question you have?

DaveW

----

Thanks for all the answers, I did finally solve the problem though. I had accidently set the receivePort: to the same as the sendPort: on the client (where receivePort: should be nil) and the oppoisite on the server =)

 AntonKiland.

----

I'm trying to get my rootObject to go away after invalidating and deallocating my server connection, so that the connection on the other end gets cut off. The problem is, the rootObject sticks around, even after everything else has gone away! Any ideas as to why this would happen? I'm using NSSocketPort over IP.

----

If you're interested in doing distributed computing with Cocoa have a look at DistributedComputing.

