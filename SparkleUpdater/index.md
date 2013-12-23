---
layout: page
title: SparkleUpdater
---

Sparkle is a module that developers can stick in their Cocoa applications to get instant (five-step install!) self-update functionality. By that, I mean that your app will be able to update itself, not just check for new versions: it�ll read the update information from an appcast on your server, download, extract, install, restart, and even offer to show the users release notes before they decide if they want to update.

You can download it and see a movie of it in action at http://andymatuschak.org/pages/sparkle.

----

Eww.. windows made from code? Why not bundle it all into a framework?

----Eh, frameworks are unwieldy. This is much faster and has much less inertia for the guy using Sparkle. At least, I think so, anyway. This way, the installation is kept, really, really easy. Just a lot more work for me. -- AndyMatuschak

----Tangent -> WorkingWithFrameworksDiscussion

----

I do not have tested SparkleUpdater yet but i find this idea really interesting. But I wonder, does Sparkle supports localization? I mean, in every aspect (including release notes) ?
----
The latest beta does, simply include localized URLs in InfoPlist.strings and you're set. However, localization of the strings still needs polish (NSLocalizedString should be added here and there to allow genstrings to work and localization to happen). -- EmanueleVulcano aka millenomi
----
Whoo, didn't notice it.     genstrings -s SULocalizedString *.m; mv Localizable.strings Sparkle.strings does the trick. -- EmanueleVulcano aka millenomi

----
Hi, Andy. I read your blog and appreciate your efforts on this project. This is a nice idea that seems nicely executed. Keep the good work! I was also just reading the above and wanted to add that Sparkle **is** now a framework, right? Ah, ah!! Why did you change your mind, I would be interested to hear your reasons. Thanks!

----I changed my mind when I started needing to include a bunch of libraries and frameworks for the features I wanted: Security.framework, WebKit.framework, and libcrypto. It would have been lame to make the host app take care of linking those in. -- AndyMatuschak

----
The download at http://sparkle.andymatuschak.org/ appears to be down. Are there any known mirrors?

----AndyMatuschak is studying at Caltech and his trac/subversion server is not always up. I understand that Sparkle is currently at release 1.1 (see http://ironcoder.org/blog/category/sparkleplus/ ). Many projects have the source to Sparkle in their repository (for example, http://svn01.23i.net/chatkit/trunk/Utilities/Sparkle/ ), and the source is unlikely to disappear. You can probably get the framework from http://www.macupdate.com/info.php/id/20366/sparkle or http://www.versiontracker.com/dyn/moreinfo/macosx/28827 .

----
I've noticed that Panic's new app Coda uses the framework, but the box that pops up looks significantly different. How/Why did they go about doing that?

----
FYI The code is, as of even date, available from http://sparkle.andymatuschak.org/ ; it is at revision 34 .

