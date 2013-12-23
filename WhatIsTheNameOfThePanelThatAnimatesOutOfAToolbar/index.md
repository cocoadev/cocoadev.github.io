---
layout: page
title: WhatIsTheNameOfThePanelThatAnimatesOutOfAToolbar
---

What is the term of a alert panel that animates out of a toolbar, like the one found in Mail.app when it asks a confirmation when you delete the thrash content? It's kind of like an alert sheet but instead of simply pulling down it leaps out...

----
That's just a regular old sheet. The difference in animation is simply because there are two different animations, one for sheets that are smaller than their parent window and one for sheets that are bigger.

----
But the alert in mail.app is smaller than it's parent... Here's a screenshot to explain what I mean...

http://img248.imageshack.us/img248/6697/mailalerttl0.jpg

Although I see that the animation is different depending if the toolbar is collapsed or not. 

----

Looks like a standard General/NSAlert window to me.

----
It's all just standard sheets. The exact animation and look of the sheet depends on the relative sizes involved as well as the attributes of the sheet and the window it is attached to.

----

To go into further detail, a sheet "pops out" if the window: 1) is smaller than the sheet width-wise, or 2) the window is metal or has the continuous toolbar/title bar style masks set. Otherwise it just pulls down, which only happens in original Aqua windows without toolbars or with pinstripe toolbars. --General/LoganCollins
