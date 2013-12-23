---
layout: page
title: KeepiTunesDataBaseUpdated
---



Hi,

I'm currently making a kind a music player that would be able to use the iTunes library.
But there's something that messed me up : I really want to keep smarts playlists up to date because my soft doesn't need iTunes to be launched for playing songs. The most important fields from the iTunes Music Library XML file are "Played Count" and "Last Played" or something like that. 
Anyway, they are important for "recently played" and "most played" playlists.
So I tried to modify a track played count number with Property Plist Editor (iTunes not launched) and then I've saved the file. I've  opened iTunes but the track played count is still the same than before editing the XML file.
I tried songbird, but it doesn't keep those fields up to date in iTunes too.
I also don't want to use AppleScript for changing those information because it would mean that iTunes has to be launched for that.

I suspect that Apple keeps tracks informations somewhere else, like caching them before quitting.

Any idea of what can I do about this ?

Thanks,
Louka - a French developer.

