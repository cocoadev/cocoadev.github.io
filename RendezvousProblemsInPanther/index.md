---
layout: page
title: RendezvousProblemsInPanther
---

Please help! I've been struggling with this problem all day and have yet to find a solution. I have narrowed the problem down to this:

In Panther only, if you immediately     resolve an address after the NSNetService has been found, then it will resolve it to the address that the service was last used under. However, if you resolve a service about a second after that, then it will resolve under the correct address. Try this:

1. Build the PictureSharing and PictureSharingBrowser projects in Panther. (located at /Developer/Examples/Foundation/)

2. Run both applications on the same computer (or two different computers if you are close enough to another one - I have tried both ways). Note, if you run it on two different computers then make sure the browser is running on Panther; the other one can be on Jaguar or Panther.

3. Click "Start" on the PictureSharing app to establish an address, then click "Stop".

4. Click "Start" again and quickly go to the PictureSharingBrowser and click the listed service. This must be done within a second or so. If you did it fast enough, the picture should not show up and the given port will be incorrect (it will be the port of the previous service).

5. Deselect the service and reselect it. The picture will show up and the port will be correct.

This isn't just a bug in PictureSharing, I've experienced it in several other Rendezvous applications. It's not too much of a problem if the resolve is triggered by a user event such as in PictureSharing. However, in my application I want to programmatically resolve the address immediately after the service has been found. Does anyone know a decent work around to this problem? I would prefer not using a timer and waiting for a couple seconds before resolving the address.

-- RyanBates

Just as an update. This problem was fixed in 10.3.2. However, if you want to support a previous version of panther, you still need to use a timer and delay for a second or two before resolving the address. -- RyanBates

