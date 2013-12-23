---
layout: page
title: EmbeddingOgreKitFramework
---

I am having no success in embedding the OgreKit framework in my application. I downloaded OgreKit_2_1_2 from http://www8.ocn.ne.jp/~sonoisa/OgreKit/. Built the included targets which comprised some test utilities and a framework. I added the OgreKit.framework to my Copy File folder in the XCODE Target directory of my new XCode project. The build succeeded, however, when I ran the application, the debugger could not find the library. I refered to the ApplicationLinkingIssues page and used the Copy File recommendation, but no luck. Is there something I'm overlooking? - patrick cusack

----

From what you have told us you need to check 1) That the framework has been embedded - this may be overkill - but I suggest using the context menu on you built .app to confirm that there is a Frameworks folder inside the Contents folder and that it contains a copy of the framework; 2) The exact error message that you are getting; 3) That you have the proper "installation Path" per http://cocoadevcentral.com/articles/000042.php and 4) Check with otool that the application and library are linked correctly per MoreOnEmbeddingFrameworks. Hope that this helps and comes across in the right way!

----

these resources helped immensely. I was making a stupid mistake on the copy files phase.

