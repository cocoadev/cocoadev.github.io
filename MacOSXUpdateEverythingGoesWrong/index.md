---
layout: page
title: MacOSXUpdateEverythingGoesWrong
---

Sorry, it's somewhat "off-topic", but since I could not find any help, I try here because I know people will answer kindly.

I updated to Mac OS X.2.8 recently, and since then, everything seems to be screwed up. Many apps, including Safari and QuickTime suddenly quit with no apparent reason. And the most important Cocoa project on my HD doesn't work since the update. I get at least 94 errors when I try to compile. Most of them look like this :
----
    /System/Library/Frameworks/QuickTime.framework/Headers/QuickTimeComponents.h:6168: parse error before "AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER"
----
And most of the other errors refer to a line of code somwhere in the QuickTime framework. Like this :
----
    In file included from /System/Library/Frameworks/QuickTime.framework/Headers/Movies.h:24:
----
I tried to delete the reference to the QuickTime framework and reinsert it in my project, but no result...----
Is it possible that the update could have been corrupted because I was running other apps in the background ?
Anyway, thanks for your help...

-- Trax
----
I also have the same problem (apps not opening) after updating to 10.2.8 and Quicktime 6.4.
----
Last week I installed both the 10.2.8 update and the QuickTime updates. Since then I can't open Quicktime or Appleworks, as well as ALL non-apple applications: Internet Explorer, Microsoft Word, PowerPoint, Stuffit, Roxio Toast, OmniPage Pro, etc. They bounce in the dock for a while, and then give up. The updates were done via Software Update, and I too wondered if something happened during the update. I've tried downloading just the QuickTime updater and reinstalling that to see if it would help -- it looks like it's installing, but the apps still don't work. Most Apple apps (iChat, iMovie, iPhoto, iTunes, Text Edit, Safari) still work. Any suggestions on how to fix this?
--Joyce
----
The quicktime update installed some new headers, and it looks like it is expected to be compiled on 10.3 only-

I've seen the problem on mailing lists, but I don't really have a solution.  However one person on the carbon mailing list offered this solution:

#ifndef AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER

    #define AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER UNAVAILABLE_ATTRIBUTE

#endif

http://lists.apple.com/mhonarc/carbon-development/msg12077.html

user: archives

pw: archives

-- GusMueller
----
Yes, the problem is QT 6.4 update.  The solution is to reinstall 6.3!:
http://docs.info.apple.com/article.html?artnum=120255
-Ken

