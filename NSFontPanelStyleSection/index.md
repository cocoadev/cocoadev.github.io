---
layout: page
title: NSFontPanelStyleSection
---

In Panther, Apple added a new section to the top of NSFontPanel that contains controls to set text shadows, colors, and various other things. Sometimes it's not useful. Is there any way to turn it off? I have scoured the documentation and have not found any way to do it.

-- MikeAsh

----

According to NSFontPanel, you might want to try just overriding changeAttributes: to do nothing wherever you don't want style changes? Not really good UI, though...

-- UliKusterer

----

I'm already implementing all of this stuff manually, so in this case, implementing changeAttributes: to do nothing is the same as not implementing it at all. I agree that it's not good UI, which is why I'd like to hide it, but it doesn't seem possible. -- MikeAsh

----

If you're reimplementing the font panel, why not just go ahead and reimplement the rest of it? *That's not what I mean. Instead of simply showing the font panel and having it affect a text view, I'm pointing it to my custom controller class which handles font changes for the app. I'm not reimplementing the font panel, I'm manually implementing the response to the font panel's messages.*

