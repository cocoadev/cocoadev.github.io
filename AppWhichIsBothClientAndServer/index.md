---
layout: page
title: AppWhichIsBothClientAndServer
---

Hi all.

I have the need to add a networking facility to my app, I wish to be able to transfer an NSData object from one application to another, and have the appls see all other apps on the network via rendezvous.

Looking through the Cocoa examples, the PictureSharing example was great, its simple and concise. It also does exactly what I need, publish and browse services via rendezvous, and send NSData objects over the network. But, the problem appears when you consider that the Picture Sharing example is split into two apps, a browser and a server.

This wasn't too good for my plan, but I have hacked together a mutant application that is basically the two apps merged into one. It (the source) can be found on the following site:

http://homepage.mac.com/peterlaurens/development/

It is named ObjectShareTest.zip.

There is a problem that I do not understand, the app works until you connect to another service, and THEN the app that *connected* to the service goes and stops sharing itself. I think this must be some kind of port conflict error. 

I am very new to the whole BSD networking thing, and have only a slight idea of the code used in the Picture Sharing example, but have managed to hack them together any way. I think it must be something to do with sockets conflicting (using same socket to broadcast the service and connect to other services?).

Any help on merging PictureSharing into a single app would be much appreciated, I'm confused!

Thanks

-Nex

----

O'Reilly has a great tutorial on creating a Server-Client application using Rendezvous with Cocoa. Check it out here:

Part 1: http://www.macdevcenter.com/pub/a/mac/2002/11/08/cocoa.html
Part 2: http://www.macdevcenter.com/pub/a/mac/2002/11/15/cocoa.html
Part 3: http://www.macdevcenter.com/pub/a/mac/2002/12/26/cocoa.html
Part 4: http://www.macdevcenter.com/pub/a/mac/2003/05/13/cocoa.html

It worked pretty well for me, but I still had some problems. Check RendezvousProblemsInPanther for one of them. So far I haven't found a good solution for that, I'm just using a timer that waits for two seconds before resolving the address. Also, if the application ever sends a message to itself, it is best to bypass the network stuff altogether because I've had problems with that too. If you are sending large files over the network, you may also want to put the sending method on a seperate thread so it doesn't hold up the user. Hope that helps.

-- RyanBates

----

Thanks for the links. Been looking around for awhile.

-- JohnDevor

