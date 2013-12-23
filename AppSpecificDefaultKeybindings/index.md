---
layout: page
title: AppSpecificDefaultKeybindings
---

The documentation says:

*

In most instances, the interpretKeyEvents: approach is preferable to the interpret-yourself approach. [...] specify alternative key bindings in a dictionary.

*

In my application I define a lot of new action methods which the user may wish to bind to keys -- he or she could change ~/Library/KeyBindings/DefaultKeyBinding.dict, but that's probably not desirable, furthermore it prevents me from shipping my application with a default "extended" set of key bindings (instead I would require the user to copy/merge a template).

So how do I provide a dictionary for the interpretKeyEvents: to look in (preferable before the default key bindings and system wide standard key bindings)?

Project Builder is said to merge all key binding dictionaries to create a composite dictionary. But this raises the same question of how to hand it over to interpretKeyEvents: and does PB hard code the path of the various key binding dictionaries the system searches?

