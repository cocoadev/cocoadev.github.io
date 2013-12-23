---
layout: page
title: OSXUserExperience
---

 - OSX User Experience




*Apple to add all the Cocoa classes to AppleSpell, to ease spell checking of text files with source code in them. (yea:2 nay:1)


----
Only if AppleSpell contained a separate dictionary, say CocoaSpell, as an option; completely configurable from the Services menu, an NSStatusItem, or from a Preference Pane. http://goo.gl/OeSCu
----

And, if Xcode would just play nicely with CocoaSpell, and let you choose a correct option with out having to display the Spelling panel.

----

This is a punch for all speakers/readers of Japanese (And quite possibly other asian languages.):

NSText: Furigana (Ever read a Japanese manga or book? The small characters above or to the side of Kanji in some (especially childrens) books.) OS X has some kind of list over common compounds and their readings, so it shouldn`t be impossible to pull of?

This is more a user thing, I know, but maybe a possibility for some of the skilled programmers around this site?

*Isn't this called ruby text? OS X supports that.*

Some groups refer to it as ruby text... It may be /supported/ but I haven't found an option anywhere to enable/disable/edit/add/remove or in any way do anything with furigana, and it would be a boon to me to know how... especially if it can be enabled in ANY NSTextView...

(No report filed with Apple)

AndersTotland

Basically impossible working backwards...kanji have MANY different pronunciations depending on the compound, so a dictionary of ALL common compounds would be necessary. HOWEVER, reading the user's input and displaying it above the kanji wouldn't be as hard...just harder to store internally. Maybe a solution using NSAttributedString would be in order... --JediKnil

*But Kotoeri already does it: select kanji, press option-shift-R or control-shift-R, see the kana.*

----

It doesn't seem that impossible, since Kotoeri already needs such a list in order to transform my input of, for example, "otona" into the correct characters.

----
Here's a reference to ruby text that makes me think it must be supported to some degree.

http://developer.apple.com/documentation/Carbon/Conceptual/ATSUI_Concepts/atsui_glossary/chapter_11_section_1.html

----

Something I've done twice in the past couple of days: Dragged a running app's Dock icon to the trash can to quit it. Since Dock items only have a tenuous connection to filesystem objects, I don't see why this couldn't be added to the UI.

*Hmm.  I don't think that makes any sense.  What does the trash can have to do with quitting an app?  Answer: nothing.  It's already bad enough that we drag disks to trash to eject them (and at least with os X apple changes the icon when you do that).  What makes sense to me is that a dock icon should be precisely the same as an alias.*

It only doesn't make sense by the old definition of the Trash as a property of the file manager (Finder). This isn't so in OSX - by virtue of its presence in the Dock, it's a universal UI element for saying 'get rid of this'. I've been a Mac user for over a decade, and did this unconsiously a couple of times. Not once did I fear deleting the application from my disk.

I know this goes against most dock-haters' rants, but we should be using the OSX Trash for *more* things. Dragging a toolbar item to the trash should remove it from the toolbar. Dragging selections to the trash should perform a cut operation. What sense does it make to have a UI element that is always visible, always available, and only usable from one application?

*Er, no. Dragging a selection should **clear** it from the document, and leave a clipping in the trash. This is in the HIG.* <-- See! Even Apple thinks the trash should have more things to do.

*hear! hear!  Although... I will say this, on Apple's smallest sized screen (currently 20"), it's a long way from the top of the screen to the bottom... a reusable trash can UI element can turn out to be a real <pun>drag</pun>* 

Nobody's saying it should be the **only** way of doing things. But if it's there, it might as well serve a purpose.

*Um? At what point has 20" been the smallest screen? There are still 1024x768 12" *Books.*

20" is the smallest one you can buy just like that.
----
Support for keyboard-driven menus like Windows -- it's one of the bits I miss most. Yeah, I know you can /technically/ do that with ctrl-F2, but if it takes 12 keystrokes to get to the menu item you want, you might as well hunt for the mouse.

When talkling about keyboard support for menu, how about keyboard access to context menu, as it is standard on the Windows platform. Although I am hunting for this keystroke for years now, I have not yet found one. Is there one? I don`t think so.

