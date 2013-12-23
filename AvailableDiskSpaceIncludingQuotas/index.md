---
layout: page
title: AvailableDiskSpaceIncludingQuotas
---



Hello everyone. I'm working on a simple NSStatusItem which will display the available network storage space to users in realtime. The thing is, users have space quotas. I have tried using getattrlist() and statfs() but both just return the total space on a volume available to all users. Unfortunately I am an old C guy and I am just learning the Objective-C and the Cocoa framework. I know this information is available somewhere because the Finder displays the user's available quota space. For example, if there is 200GB left on the server, but only 500MB for that user's quota, the Finder will show 500MB. Ideally, I would like whatever solution I find to work with and without quotas (like the Finder) and on local and remote volumes. 

By the way, the server is mounted via AFP. 

----

Have you tried FSVolumeInfo funcitons (in CoreServices.framework)? 
CFURLGetFSRef
FSGetCatalogInfo
FSGetVolumeInfo

----
Actually, I think you'd have to ask Directory Services, since I believe quotas are managed that way.

