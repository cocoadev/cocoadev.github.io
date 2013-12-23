---
layout: page
title: UserConfigurableHotkeys
---

I wish to allow the user to customise the contents of a menu, this include binding hotkeys to the items. But how can I snoop the keys pressed *before* the system resolves menu keys? I know both IB and PB does this, and they should both be Cocoa...

Also, does anyone have a map of which glyphs to use for tab, arrow keys, enter, delete, backspace, page up/down, home/end, F1-14 and similar?

----

The only way to pre-screen key events that I know of is to subclass General/NSApplication and override the -sendEvent: method.  Because of the way the that the General/NSApplication object is created, you'll have to use -poseAsClass: to get it to use your subclass.
  
OTOH, if you're just letting them change a menu item's key equivalent, you can use General/NSMenuItem's -setKeyEquivalent: and -setKeyEquivalentModifierMask: methods instead.

Most of the keys you're looking for are listed in the General/NSEvent.h header and the rest are ASCII codes (i.e. 0x7F for delete, 0x09 for tab, etc.).  -- Bo

**Can't you just set your program's General/PrimaryClass (or whatever it's called) to General/MyNSApplicationSubclass rather than having to use posing? I thought General/NSApplicationMain() took that into account.**

It's  *Target Settings - Info.plist Entries - Cocoa-Specific - Principal class:*

Worked fine last time I tried. :)

----

Doh.  Learn something new every day.  -- Bo

----

Thanks for the info, it gives me the events I need -- I even get "command backspace" which for some reason never make it to my custom view's keyDown!?!

With regard to the keycodes, I was referring to the glyphs used to visually show the keys, i.e. a tab-stop for tab, not the actual keycode (found in the headers). I can go hunt for these myself using a font editor, but was just hoping someone already had a list.

Now a new problem has arisen -- I'd hoped just to do a General/HotkeyTextField subclass (of General/NSTextField) which would translate the keys pressed to glyphs, but it seems that it's not that easy, since General/NSTextField itself leaves the editing to the field editor, which is an General/NSTextView -- so I cannot just overload becameFirstResponder/resignFirstResponder, and register my sendEvent:-hook (provided by my custom Application object) between these.

Perhaps the best way is to recreate the wheel by subclassing General/NSView and doing it all by myself (drawing frame, focusring, text etc., perhaps using General/NSTextFieldCell for some of it)?

oh, and also a big "thank you" to Bo for letting us know that even he can learn new tricks :-)

*
Okay, I have created my own view subclass but now have a problem: how do I tell if I should ignore shift for the recorded key?

E.g. "shift $" produces � (on my keyboard), and "charactersIgnoringModifiers" will give that latter character, so this key should simply be recorded as �, whereas e.g. "shift return" will not produce a special character, and should indeed be recorded as "shift return".

It would seems as shift should be ignored for all other than 'special' keys (but with tab as a non-'special' key and space as a 'special' key, plus all the keys on the numeric keypad, except ',' ('.' on the US keymap)). But this is with my own keylayout...

Hardly any comfort, but at least IB and PB are not consistent in neither their glyph usage nor mapping of key strokes to a given hotkey, but it shows that there seems to be no real/documented/supported way to introduce customisable hotkeys into your application -- but then again, why should there when Apple clearly disfavour these..
*
