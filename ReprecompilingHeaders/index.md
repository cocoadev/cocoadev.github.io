---
layout: page
title: ReprecompilingHeaders
---

I recently installed Safari 1.0 (basically as soon as it was available), and since I haven't been coding much lately, I only just noticed that its changes to Foundation broke my code. Well, the actual breaking stuff was easy to fix, just typecasts, and although it's bloody annoying, I suppose I can live with it. What I'm not wanting to live with is the fact that the precomps are all broken now.

I haven't updated to GCC 3.3, I'm still using 3.1. This problem is making my builds take forever.

Should I upgrade to 3.3 and use the new precomp system as described on UsingPCHWithGCCThreeThreeAndProjectBuilder? Or is there an easy way to fix the precomps for Foundation?

Thanks,

-- RobRix

----

This is something that I would like to understand too.  I took a look at 

CocoaDevCentral ( http://cocoadevcentral.com/articles/000062.php )

I downloaded the sample projects for server and client, but could not get them to run properly.  First, the client program was missing the server.h file (which was not such a problem, because I just copied it over from the server project).  But when I run either of them I get the warnings 

Server.h:10: warning: could not use precompiled header '/System/Library/Frameworks/Carbon.framework/Headers/Carbon-gcc3.p', because:

Server.h:10: warning: 'CarbonSound/CarbonSound.h' has different date than in precomp

Server.h:10: warning: 'CarbonSound/Sound.h' has different date than in precomp

----

    sudo fixPrecomps at the command line should do the trick.  -- Bo

Thank you, Bo. I figured there was something like that-- heck, I've seen it before-- I just couldn't remember the exact command and my searches weren't turning much up. -- RobRix

