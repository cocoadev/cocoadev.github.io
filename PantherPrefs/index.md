---
layout: page
title: PantherPrefs
---

How did the System Prefs get those dark boxes? --JoshaChapmanDodson

*See NSToolbar's toolbarSelectableItemIdentifiers and related methods*

Josha, just a suggestion: more detailed questions (for example, "How do I highlight toolbar items like System Prefs does?") are more likely to get better/more detailed answers. I figured out what you meant, and obviously someone else did too, but it's best not to leave it ambiguous.
----
Thats not what I meant. This is what I meant.
http://pixikon.com/images/dark.jpg
Because the InterfaceBuilder boxs are rounded with shadow.

--JoshaChapmanDodson
----

Ah, that clarifies a lot, thanks. Unfortunately, I'm not sure I can help with that, because I'm not seeing anything in initial toying with InterfaceBuilder, and SystemPreferences' MainMenu.nib is not helpful either. It looks like they're doing it programmatically; you could probably achieve similar results by making a view class which would fill certain areas with an NSColor that was black but with only 50% (adjust to taste) alpha transparency, and probably you'd have to override     -isOpaque: to return NO.
----
Thanks for your help, I'll try that. --JoshaChapmanDodson

It might just be a box extending past the borders of the window �DustinVoss

Thank you, Dustin, for thinking outside the box :D (But seriously, I don't think so, due to the shadow of NSBox instances.) -- RobRix
----
Mabye I could create a rectangular image (black), and set to a NSButton, and uncheck enabled. 

--JoshaChapmanDodson

----
Have you tried working directly with NSPreferences? It seems to offer a much more usable interface (IMHO) and requires very minimal code to get it up and running. Yes, it is undocumented, but you get a smooth looking prefs interface (� la Safari) without too much trouble - and the internal interface has been stable for quite some time.  I can post some example code if need be.  --MikeSolomon
----
Could you please post some code?
----
It's documented! Here file:///Developer/Documentation/UserExperience/Reference/PreferencePanes/ObjC_classic/Classes/NSPreferencePane.html

--JoshaChapmanDodson

----

Josha, that has absolutely nothing to do with your question. NSPreferencePane is a class used when developing new preference panes, for any application which can host them. Examples of preference panes: Appearance, Expose, International, etc.

I'd agree with the suggestion to create a custom view that does what you require. Your NSButton suggestion is just... wrong. That's not what buttons are used for, and that's a very "hacky" way to do it.

--JosephSpiros

----

Not sure if this is what Josha is wanting to do (make a preferences window that emulates System Preferences look), but Colloquy use to do this. (Also still used by Fire and Comino.) With 3 custom classes that read in prefPane bundles and display them like 10.2's System Preferences, you too can be cool. With keyboard navigation, and the whole nine'. (It would have to be updated for the 10.3 look, and I would suggest exactly what someone else said, a 50% black alpha fill. The code can be found in Colloquy's attic (since Colloquy now uses NSPreferences).

http://cvs.sourceforge.net/viewcvs.py/colloquy/colloquy/Attic/

Files are MVPreferencesController.m, MVPreferencesGroupedIconView.m and MVPreferencesIconView. Some other misc files are used, and are scattered throughout other Attics.

�TimothyHatcher

