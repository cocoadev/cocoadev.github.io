---
layout: page
title: CURLHandleBuildProblems
---

I've been having an awful amount of trouble attempting to build a project that links CURLHandle. Each time I build the projct I receive an error in CURLHandle.h that says, "curl/curl.h: No such file or directory." I don't know what the problem could be. I've been able to use CURLHandle in the past. Perhaps there is a problem in Xcode, or Panther, that needs to be fixed before building?

I have tried downloading the latest CURLHandle source, but still run into the same error. Any help would be greatly appreciated. Thanks!

EricCzarny

----

See this thread: POSTMethodANDNSURLRequest

----

I don't think this is quite the same problem.  For this, you really just need to make sure that     /usr/include/ is among your header search paths, as the Apple-supplied header (found at     /usr/include/curl/curl.h)  should work fine.  At worst, you could just add that file to your project, and switch the     #import <curl/curl.h> statement to     #import "curl.h".   -- Bo

----

I'm getting similar problems trying to build a project using CURLHandle (the CURLHandleTester project):

1. I know this is probably a stupid question, but what dependencies does this framework have? Do I have to already have curl and libcurl installed?
2. I'm assuming that I need to install the CURLHandle.framework into /Library/Frameworks, correct?

The problem is, that even though the CURLHandle project compiles fine, and I copy the resulting framework binaray into /Library/Frameworks, I get an error when trying to build and run CURLHandleTester:

Tool:0: Command /System/Library/PrivateFrameworks/DevToolsCore.framework/Resources/pbxcp failed with exit code 1
Tool:0: /Users/DeadJB/Desktop/CURLHandle (All Sources)/CURLHandleTester/../Frameworks: No such file or directory

So I tried to create a Frameworks directory in the parent folder, and then I copied the CURLHandle.framework file into that directory. Now I get these errors when trying to build and run:

/Users/DeadJB/Desktop/CURLHandle (All Sources)/CURLHandleTester/TestController.h:5:34: error: CURLHandle/CURLHandle.h: No such file or directory

/Users/DeadJB/Desktop/CURLHandle (All Sources)/CURLHandleTester/TestController.h:6:41: error: CURLHandle/CURLHandle+extras.h: No such file or directory

So what should I do to solve this problem? I feel like I'm missing something obvious, so please forgive me if this question is so trivial.

