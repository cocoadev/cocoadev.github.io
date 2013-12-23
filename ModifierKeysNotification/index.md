---
layout: page
title: ModifierKeysNotification
---

Hi.

I want my windows not to be clickable, unless a chosen key-combination is pressed (command-option or something like that). But it has to "get" that the modifiers changed even if the app is not frontmost, and the window is not the keywindow. 

Is there a possibility to do that?

----

It's polling (ew) but you could set up an NSTimer on your run loop to fire once ever, say, 5ms. It would then call GetCurrentKeyModifiers() and perform its action depending on the result.

----

Perhaps you could subclass your NSApplication and override sendEvent:?

----
You want them to be visible while the keys are down and go back up when the user releases the keys, right? If you're willing to instead use a toggle (press Command-Option-whatever to make them clickable, press it again to not), you could use GlobalHotKeys. Going this route might make you want to just use show/hide.

Come to think of it, why *do* you want this? Non-clickable windows generally frustrate me. --JediKnil

----

You can't override -sendEvent: to capture events when an app is not frontmost, because -sendEvent: is only called when an application *is* frontmost. :)

