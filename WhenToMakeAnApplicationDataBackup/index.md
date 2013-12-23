---
layout: page
title: WhenToMakeAnApplicationDataBackup
---



I save my app's data in a keyed archive and I wonder when it is the best time to make an auto backup - right before it quits or just after launch? When are the most chances that my keyed archive can be messed up and corrupted?

*It probably makes more sense to back it up on quit, since the time between the last change (and quit) and the next run is the most likely time for problems to occur (and the longer time). The user starts the app, it backs up data, they change the data and quit. <insert time here> The user starts the app again and something's wrong and *that* gets backed up. Huh? :-) It makes more sense to back it up on quit (if the data changed) so that the most recent changes are backed up. .... right?*


Thanks for clearing my mind, I just was too tired it seems :)

----

It agree it makes more sense to back it up before it quits, however, if backing up your data is a lengthy process then you're going to have some problems, if the user is trying to turn off his computer and the application just starts making a backup he's gonna get annoyed. So be sure to at least let the user cancel the backup process in case he's on a hurry. -General/PabloGomez

----

No, it's not lengthy, just a copy of single file.
