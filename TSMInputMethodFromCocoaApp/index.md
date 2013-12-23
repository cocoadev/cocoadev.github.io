---
layout: page
title: TSMInputMethodFromCocoaApp
---

I'm wondering if it's possible to create a General/InputMethod (I think I'm using the right term here) in a cocoa application-
I've checked out the General/BasicInputMethod sample ( http://developer.apple.com/samplecode/Sample_Code/Interapplication_Comm/General/BasicInputMethod.htm ) from apple's site , and I can't really make any sense of it (lots of carbon code I just don't understand).

What I want to do is have my application insert text based on a hotkey, but I want it to work for both carbon and cocoa applications (so the General/NSInput* stuff is out)

Does anyone have any suggestions? (sample code is good too!)

thanks- General/GusMueller

----

Input Methods are very tricky things to write (part of my product, Spell Catcher X, is  an input method). If there's some other way to do what you need, try to find it.

If you just need to insert text into any arbitrary app in response to a hot key (one that can be defined using the Carbon hotkey General/CarbonEvents), then you shouldn't have to write an input method. Handling hotkey General/CarbonEvents in Cocoa apps is well documented, there are samples floating around, I'm sure you can find something.

The second part, actually sending the text to the app is potentially MUCH harder. If the length of the text being inserted is under your control and not generally going to exceed 200 characters or so, you can use Core Graphics General/CGPostKeyboardEvent stuff to simulate keyboard events. That's pretty well documented as well.

If you have to insert more text than that, you'll have to resort to other methods. General/AppleEvents are a possibility, pasting is another. Just note that most Cocoa apps behave similarly when you do stuff like this, but every Carbon app has its own little quirks.

If you have to be able to handle keyboard input from the user while you are inserting longer strings into the app (i.e. NOT just discard it - suppress the keystrokes, buffer them, then re-post them once you're done so that user input is not lost and not intermingled with your posted characters) then you'll probably have to write an input method - and a relatively complex one at that.

If you need to be able to insert Unicode text into Carbon apps that don't handle Unicode input, you may also have to write an input method or at least find some way of determining the text input capabilities of the active app and convert to a Mac OS encoding from Unicode.

Hope that helps,
Evan Gross
(General/SpellCatcherX author)

----

That helps, thanks-

Right now I'm saving what is on the clipboard, putting my stuff on there, and using General/CGPostKeyboardEvent to paste it in, and then restoring what was on the clipboard- it just feels wrong however...

I've looked a bit into the apple events, and figured out that maybe I want to do something with { kEventClassTextInput, kEventTextInputUnicodeForKeyEvent }, but beyond that I don't know.

Well, from what I've read, and what you said- I'll probably just stick with the pasting of the text.

thanks,

General/GusMueller
