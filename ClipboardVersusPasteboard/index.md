---
layout: page
title: ClipboardVersusPasteboard
---

Google says:

*Results 1 - 10 of about 203 from developer.apple.com for clipboard.*

*Results 1 - 10 of about 129 from developer.apple.com for pasteboard.*

Personally I started to use pasteboard after switching to Mac, but I do see the word clipboard in some applications. So what's the recommended word?

Seems the Carbon documentation also refers to the clipboard as the 'scrap'.

----

NeXT people say pasteboard, Classic people say clipboard. The terms are interchangeable as far as developers are concerned (barring religious API wars.) I would use clipboard in the UI, since it's what most Mac users are familiar with.

----

I would second that. Use the word 'clipboard' in the UI, just like you use 'restart' instead of 'reboot' etc. The clipboard API in Carbon is called the Scrap Manager, so in technical docs it's called the scrap. (Although there is a new Carbon API in Panther for accessing the Pasteboard).

----

The Finder has a Show Clipboard command. I think that's about as official a source for terminology as we're going to get.

