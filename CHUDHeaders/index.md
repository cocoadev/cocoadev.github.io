---
layout: page
title: CHUDHeaders
---

I'm trying to use the CHUD framework to turn on and off profiling of my code by Shark (chudStartRemotePerfMonitor). I can't seem to find the framework nor headers on the 10.4 sdk. I can see it in the 10.3 SDK. Any tips on where it could be hiding? I ran the CHUD installer and it said I had everything installed already. Any ideas on what I need to do to get the headers in place?
Thanks,
Jason Rusoff

----

/System/Library/PrivateFrameworks/CHUD.framework

----

Thanks, I see it there. But I don't see the header files. In the 10.3.9 SDK there is chudRemote.h in ChudCore.framework/Versions/A/Headers. I don't see the headers in the 10.4 SDK. I think I need that header to call into chudStartRemotePerfMonitor(). Could I have installed the wrong 10.4 SDK?
Thanks for your help. 
Jason

----

CHUD.framework in the 10.4 SDK is a PrivateFramework, meaning that the header's are stored in the framework, and aren't directly linkable. You'd have to reverse-engineer any private framework to get access to it. See LinkToAPrivateFramework. --LoganCollins

