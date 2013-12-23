---
layout: page
title: DynamicDocumentTypeBinding
---

I am working on an application where I want to pull out the file I/O layer as a set of plug-ins but I am not sure if there is any way to dynamically update the file types supported for I/O.

The only idea I can think of is finding a way to override the infoDictionary for the main application bundle but it sounds inelegant and requires me figuring out how to change the version instantiated for the application.  In short, it either won't work, or it won't work well.

Any ideas or is this really not something that can be done within the system? (if that is the case, I will just have it accept everything and do selective filtering within the app)

--JeffDisher

----

I was able to get this to work by subclasses NSBundle and using poseAsClass to allow it to intercept the infoDictionary call and use the one returned by the super implementation as a skeleton which can then be modified.  However, the problem that I now have is that the NSDocumentController seems to cache the data that it gets from the infoDictionary with regards to CFBundleDocumentTypes and it wasn't really made to release this cache.

Any ideas of how to force a re-cache or will I have to choose between (1) re-implementing a small chunk of NSDocumentController to use dynamic document types or (2) telling the use to restart the program after each plugin is installed (since this would likely only happen once, it wouldn't be a big deal but it seems very wrong to me)?

--JeffDisher

----

This information isn't stored by NSDocumentController. It's LaunchServices that keeps the info, and it reads it from your InfoPlist, which is how it knows what you can open even when your application isn't running to supply an NSDocumentController. The only way to add new types is to write them to your InfoPlist file and touch your ApplicationBundle.

----

I know that LaunchServices uses this information as well and I can't effectively replace that data since it requires a logout to update it as well as write access to the PList.  The latter is definitely not an option and the former is terrible.  I am just trying to make the in-memory data play nicely with the rest of the NSDocument architecture for opening and saving, with respect to which kind of document something is.  This data is stored by NSDocumentController.

--JeffDisher

