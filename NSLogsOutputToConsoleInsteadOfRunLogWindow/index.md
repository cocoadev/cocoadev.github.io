---
layout: page
title: NSLogsOutputToConsoleInsteadOfRunLogWindow
---

Somehow, when debugging, my NSLog's output to Console, not to the Run Log window in Xcode. I can't seem to figure out why this has changed. I've been messing with it for a while and don't know what to do. Anyone know how to change this back? Thanks.

----

This has always been the case.  If you select "Run" or "Build and Go" the output will go to the run log.  If you select "Debug" or "Build and Debug" the output goes to XCode's debugger console.

I recommend adding a keyboard shortcut to XCode to call up the debugger console.  You can do this from XCode's prefs or from the Keyword panel of the system preferences.

----

If you mean the Console application, that's actually the default place for NSLog output. It's all a question of where stdout is pointing (running the program from the Terminal causes output to show up there). If you have Console open, output will be logged there. If you run the program from Xcode, the output will show up there. --JediKnil

(Unless I'm awfully confused, whether or not you have Console.app open shouldn't affect anything: all it does is provide a nice way of viewing /Library/Logs/Console/(username)/console.log (and any other log file you might want to show). --DavidGlasser)

That's what I thought for a long time too, but if you have the application's log file open, all NSLog calls will end up here. --JediKnil

