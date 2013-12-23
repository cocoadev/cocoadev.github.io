---
layout: page
title: AccessingEnglishDictionarySites
---

see also GrabbingOnlineInfo

I am keen to write a small app (Cocoa) that will retrieve the definition of a provided word (if it exists), but I am not sure how to utilise the online resources such as Wiktionary for this purpose. 

Does anyone know of a method via which applications other than web-browsers can request definitions etc. from a service such as Wiktionary? Or will I have to write a simple parsing engine and try and parse the normal HTML response myself instead? (This latter approach obviously not great in case the formatting of the Wiktionary pages changes and my parser will then break).

This sounds like quite a common thing to want, but... not sure how to connect to the online dictionaries, that's all.

----

Some online dictionaries, like dict.org, have specialized protocols that are designed for this kind of purpose. For services that have them, read their description of the protocol and figure out how to write code to use it.

Looking at Wiktionary's front page for ten seconds, it doesn't appear that they offer this kind of thing. If they don't, then you have no choice but to use HTTP, grab the HTML, and parse it.

----

You might want to look at WebServicesCore.

----

DICT looks like exactly what I was after, and there seem to be some decent DICT servers around (not Wiktionary unfortunately).

