---
layout: page
title: SettingAppNameAndExtensionsAndTheLike
---



Back in Project Builder there was an info view or an inspector on some item, maybe the active target or something, that gave you a single place to set things like the icon your app uses, the extensions for documents, the name and version of the application, and that kind of stuff.  I believe it's just a view to edit Info.plist.

I'm a little stumped when it comes to X Code 2: I can't seem to find out where to set this stuff.  I manually edited the plist file to set some of it but I need to know how to set that info in X Code 2.  I just know it's something that should be easy but I can't seem to locate it.

Thanks so much
CliffPruitt

----

Select the Targets tab, expand the Targets group in the outline view, click your target and press Cmd-I. Click the Properties tab. There you have it. By the way, in XCode's help, typing "application icon" yields many results, the fourth result is called "Inspecting Targets" and contains the answer to each of your questions above. Well, not *all* of them. See also: HowToChangeAProjectsName

----

Maybe the Application Icon part was a little misleading & shouldn't have been lumped in with the same question. Usually it's the file name extension I have trouble tracking down but it did answer my question. It's really a lot different thanProject Builder was (no surprise) so I guess I was just looking for something else.

