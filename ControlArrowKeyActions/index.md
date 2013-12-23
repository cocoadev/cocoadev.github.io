---
layout: page
title: ControlArrowKeyActions
---

If I implement:
    
- (void)move[Up|Down|Left|Right]:(id)sender;


Then for control arrow [Up|Down|Left|Right] Cocoa will invoke the corresponding method directly, without calling keyDown:, without calling doCommandBySelector: and without respecting the bindings made in DefaultKeybidings.dict (nor the global system keybindings).

Solution? Rename the above methods and handle them myself in keyDown (ideally parse the various keybinding files to see if the methods should be bound to other keys).

Problem: If my view is placed in an NSTabView, I do not get *anything* invoked at all for control arrow keys.

Solution? Do not place my view in an NSTabView.

So what does Apple say? *"it is by design..."*.

Unfortunately they do not wish to elaborate on this rather weird design -- not that it's the first time...

Could anyone please enlighten me to why Apple makes so many stupid designs, and why they never document it?

**Stupid is subjective. A lot of that stuff makes sense for e.g. the text view system. Not for MyFunky3DViewExtraordinaire, but hey.**

Stupid might be subjective, but this "design" is without a doubt stupid, because it's buggy, and Apple won't fix it, and their own NSTextView doesn't even use the system -- if I wanted to make a full replacement for NSTextView then I would have a really hard time if I were to stick with the documented stuff...

