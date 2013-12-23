---
layout: page
title: ClickableTextBox
---

I currently have a selection of text boxes in Interface Builder displaying the result of a user input.
I'm wondering the best way of making these text boxes clickable / selectable so additional information can be displayed about the box content?
I guess it's a more of a button with an altering display than text, as a selectable text box merely produces the 'enter text' icon.

Many thanks in advance :)

Pygar.

----
So... use a button and alter its title.

----
Okey Dokey, will give that a try.  Assumed button title couldn't be altered while App is running.
Woz gonna try subclassing NSTextField to override mouseDown.  Would that work?

----
It would, but using a button is easier since it's already done for you. There's no problem with changing a button's title while the app is running. Just watch out for sizing problems, you may need to -sizeToFit and jiggle with its positioning to make sure it's big enough to hold your text.

----
You're ignoring the OP's request for *selectable* text.

