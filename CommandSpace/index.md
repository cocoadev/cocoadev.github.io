---
layout: page
title: CommandSpace
---



I have an application in which I want Cmd-Space to trigger a menu, but I cannot seem to set this in IB. Is there any way to work around this? Is there some magic reason why this isn't allowed?

----

Isn't that the "change languages" global system command or something?

*Is there any way to override it in a particular app? This is an awfully ambiguous key combo to be using it as a specific global. Using 'space' is the standard way of starting and stopping audio. But what if there's an app where you'd want to start/stop audio quickly (with the keyboard) while also typing normal text? Granted, it's not a particularly regular occurrence, but in my case, it's a necessity. Recommendations?*

----

Overriding global key commands is one of the easiest ways to infuriate your users (like Docoa Browser's annoying inclusion of a service - something it didn't need to provide - with a cmd + shift + ~ combination).  Most key commands are ambiguous.  Why not use something like cmd + option + space, or something like that?

----

There are times when it is necessary to override certain 'expected' actions with others (Apple's own apps do this from time to time with the interface in general). This is one of them.

At any rate, the answer was rather simple - I subclassed NSWindow and overrode     -performKeyEquivalent: to intercept Cmd-space combos, ignoring the rest. Of course, this could be a user option ... but I'm 90% sure most users would rather have the more convenient Cmd-Space combo for start/stop actions while typing text. It's quicker and requires no *fingrobatics* to operate. Typing speed is the key here. Heeding the warnings I've read here and other places about the International switching global (cmd-space), I will allow an option in prefs to watch for  cmd-opt-space instead.

----

Don't use Cmd-Space. It will be the default key combination to bring Spotlight to the front in Tiger. -- l0ne aka EmanueleVulcano

----

So Apple is overriding their own global keys, eh? Well, so much for 'best practice' ... ;-)

... oh, and just in the off chance nobody says it, "NDA! NDA!"

----

Back to the whole best practice discussion (without the attitude, please), packages like Adobe Creative Suite overrides nearly ALL the standard key equivs. I can't tell you how many times I've hit Cmd-M in Photoshop to avoid the boss' scrutiny, only to have the Curves & Levels box come up. D'oh! But the thing is, for someone who works in a very focused manner in Photoshop all day long, I can see where you'd prefer to have a whole lot of keyboard shortcuts to use it, even at the expense of global equivs. Lesson: *If and when it makes sense, do it. Just make sure the good outweighs the bad. Apple breaks their own rules and opinions are like a-holes (yadda yadda) ... both those hackneyed sentiments very much apply here, too.*

----
For photoshop, the problem is probably that the keyboard equivalents were all set up back in Mac OS 9.  In Mac OS X Adobe had a problem: Hack off old-school photoshoppers by changing shortcuts, or hack off new users by using standard shortcuts for non-standard tasks?

BBEdit has the same problem, but the first time you use one of the collision shortcuts (e.g. cmd-T) it pops up a dialog and asks how you'd like to bind the key.  (Incidentally, as an old-school BBEdit'er, cmd-T for replace-and-find-next is ingrained in my fingers.  I chose the non-standard shortcut.)

