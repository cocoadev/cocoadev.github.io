---
layout: page
title: LaunchServiceCaches
---

This page is an attempt of getting an overview of how Finder finds an application, the reason is that I often end up with "corrupted" caches, i.e. Finder wants to start my application from a path which is no longer valid (e.g. the trash, a temporary directory created when packaging the application etc.) or it uses the wrong document bindings.

I have not found any documentation on the subject, so take this page with a grain of salt, and please contribute if you have corrections or more info.

----

If you double click a document, LaunchServices will consult     ~/Library/Preferences/com.apple.LaunchServices.plist for document bindings. This file contains both the bundle identifier and bundle signature for the application to be used -- I do not know how it then proceeds or which of the two it uses to lookup the application, but if you have some bad document bindings, delete this file.

From the Terminal, type     open -a �yourApp� I do not know how it locates the application, but I have on several occasions had     open -a not working, but double clicking documents in Finder working -- I solve this problem by deleting     /Library/Caches/*, but it looks like the actual file is     /Library/Caches/com.apple.LaunchServices.6B.csstore (the number may vary?).

----

Alternatively, you could read this: http://developer.apple.com/technotes/tn/pdf/tn2017.pdf  :-)

----

That document doesn't give any info about how LS caches things etc. -- and I'm also certain that it has changed since the tech note was written (four years go).

I often find this helpful, when LS is starting the wrong application:
    
cd /System/Library/Frameworks/ApplicationServices.framework/Frameworks/LaunchServices.framework/Support
lsregister -kill -r /System/Library/CoreServices /Applications /Developer/Applications


Unfortunately it doesn't seem to fix a problem I have where a service starts the wrong application.

It seems that internally OS X uses _many_ different ways to locate applications, and these vary in heuristics.

Ways to (indirectly) start an application:

* AppleScript � tell app �application� to�
* /usr/bin/open � open -a �application�
* Finder � double click document with an app binding
* Services � select a service offered by a program


I'm almost certain that each of these four ways have different methods to find the actual application. The problem has only grown bigger with Xcode's new individual foldres for Deployment, Development and potential other configurations.

