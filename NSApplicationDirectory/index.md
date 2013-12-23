---
layout: page
title: NSApplicationDirectory
---

Well, I think the title is somewhat self-expanatory. Is there any constant in Cocoa that represents the directory of the application itself ? When I use a NSOpenPanel or NSSavePanel, I want the default directory to be the one the application resides in. Any hint ?

-- Trax

----

    
    id mainBundlePath=[[NSBundle mainBundle] bundlePath];


--zootbobbalu

----

I think he really wants
    
    id mainBundlePath=[[[NSBundle mainBundle] bundlePath] stringByDeletingLastPathComponent];

i.e. the application's parent directory, not the application bundle itself.  -- Bo

*Thanks Mr. Obvious!! * ;-) 

--zootbobbalu

p.s. I guess that should then make the refernece mainBundleParentDirectoryPath instead of mainBundlePath - he he  
*True.  You'd think with how often I get screwed by copy-and-paste coding I'd learn, but alas no.  -- Bo*

----

And now that we've answered the technical question, I feel obliged to point out making the default directory the one the application resides in does not fit in well with Mac OS X's user interface. In 99.3% of all Mac OS X systems, applications reside in a single shared location (/Applications) and user data is stored in a per-user location (often /Users/name/Documents). Defaulting a open/save panel to /Applications will require most Mac OS X users to try to find their home directory or whatever every time they want to open or save data. The truly sad thing here ( and by truly sad, I mean 1 star on VersionTracker sad ) is that you're going out of your way to implement the wrong behavior.

Of course, if your app opens/saves applications, this "open in /Applications" business is perfectly reasonable. But how many apps do that?

-- MikeTrent

----

To make matters worse, as a normal user I'm not even allowed to write to /Applications.
You need at least admin privileges to do so.

-- AndreasMayer

----
Seems like the bundlePath is cached at launch, meaning, when you move the application while it's running, the bundlePath won't change accordingly. Is there any other way to find the path of the application? I know there's NSWorkspace's fullPathForApplicationName, but if you have more applications with the same name, it's not guaranteed that you're getting the right path. I want the path to my application that is running... Is there no easy way to do that?

----
Store the bundlePath into an FSRef sometime early in your startup sequence, then use that to generate a path when you need it; that will follow your application as it moves. However, it should be noted that a typical Cocoa app absolutely cannot survive moving while running, as NSBundle's paths do not update and so no resources will be able to be loaded after the move.

