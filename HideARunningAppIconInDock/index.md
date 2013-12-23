---
layout: page
title: HideARunningAppIconInDock
---


my new app should run as a sort of networking daemon - and should not reveal itself in the task switcher or in the dock.  how can I suppress this?

----

LSBackgroundOnly, LSUIElement

----

great! only the instructions on those pages are for Project Builder - no such menu options are available in XCode as is described there.. :-( Anyone able to help out on this?

----

Yeah, put the desired attributes in your Info.plist file. --EliotSimcoe

