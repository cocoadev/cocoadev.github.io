---
layout: page
title: LaunchAppWithinAppBundle
---

We know that Apple says that it's a good idea to just have one app bundle, for drag-and-drop install. Some apps require separate little plugins or apps, but those are contained within the app's bundle, so there's just one item in your Applications folder for that program.
I want to be able to run a small server app from within my program, and have the server's .app bundle stored within the main program's bundle. No need to hide it from the curious, just keep it in there to have the simple drag-and-drop install, and less file clutter.
How do I launch it, get it ready to recieve distributed object commands, and then kill the app safely when I'm done, all programmatically within the main app?

----

    
// will get the full path of your app if it is located in /Contents/Resources/
NSString *path = [[NSBundle mainBundle] pathForResource:@"myApp" ofType:@"app"];
 // will launch the app
[[NSWorkspace sharedWorkspace] openFile:path];


----

What's the easiest way to kill this other app once I've launched it, preferably by name? Is this possible?

----

    
system("killall myApp"); // easiest - you asked for it :-)


----
I use 
    
// pass the application bundle location as a url in the openURLs array
[[NSWorkspace sharedWorkspace]
    openURLs:applicationURLinArray
    withAppBundleIdentifier:nil
    options:options
    additionalEventParamDescriptor:nil
    launchIdentifiers:nil];

to launc the application. This method will not cause a distributed NSWorkspaceDidLaunchApplicationNotification to be posted, but it has the advantage that you can use launch options like NSWorkspaceLaunchWithoutActivation and NSWorkspaceLaunchAsync. I would just post a distributed notification in my background application if you want your main application to be notified of the application launch.

To quit the background application you can send another distributed notification or you could use an applescript like:
    
tell application "MyBackgroundApp" to quit


-- JorisKluivers

See QuitApplicationUsingAppleEvent for faster quit code.

