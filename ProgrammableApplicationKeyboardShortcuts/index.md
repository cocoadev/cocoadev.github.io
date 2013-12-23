---
layout: page
title: ProgrammableApplicationKeyboardShortcuts
---



I'm looking for the best approach to allow programmable keyboard shortcuts in my application. The ideal solution will be able to handle the following:


*Trap and handle application-level programmable keyboard shortcuts (not global hot keys), regardless of keyboard layout.
*Validate requested shortcut combo to make sure it's not already in use (preferably globally as well as within the application).
*Update any menu items containing the 'old' shortcut to reflect the new shortcut.
*The ability to allow assignment of non-standard shortcuts (such as     Cmd-Space), assuming they don't conflict with existing global hot keys. *****


***** *I understand     Cmd-Space is the shortcut for the Spotlight search window, but since this is just as **programmable** as all the other global hot keys, I would like the ability to allow such a shortcut to be assigned. I know this is a controversial subject but please refrain: this is all by **user preference** so such an argument is null and void in this instance.*

Information on this subject is rather limited and several open-source solutions I've tried either failed to account for different keyboard layouts and languages or simply didn't work correctly (I'll try to find them again to reference them directly). Any help would be appreciated -- this page could be very useful to future developers if fleshed out with a good, universally-accepted solution.

----

So question 1: Where best to begin?

---- 
As with everything, the best place to begin is the documentation.

http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/XHIGUserInput/chapter_11_section_3.html#//apple_ref/doc/uid/TP30000361-CHDIGFBH

----

With respect, it does not appear that you have read the above thoroughly (at all?). The document you referenced has nothing to do with the problem described above, but merely describes the best practice for what shortcuts to assign and what not to assign. Referencing the HIG in response to a technical 'how-to' question having nothing to do with design makes no sense whatsoever. The word 'programmable' appears in this page's title as well as in four other places in the body above your comment ... I believe I've made it clear what I'm looking for.

Incidentally, I've also found some information by searching for the term "key bindings", as they're called in XCode's preferences. There are several Cocoa documents that reference input managers, etc. This is a completely different approach from the open-source methods I've seen and frankly looks quite clunky (creating a ~/Library/KeyBindings/DefaultKeyBinding.dict file??) by comparison. By contrast, the open-source projects I've seen seem to capture and store key / modifier codes (but this has the weaknesses I outlined above). Does anyone have any suggestions as to which approach is better? If it turns out the documentation's way is the best way, does anyone know of a more detailed text outlining the best way to manage the DefaultKeyBinding.dict file / data?

----

This may do what you want. I love it:

ShortcutRecorder [http://code.google.com/p/shortcutrecorder/]

