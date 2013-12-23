---
layout: page
title: MailColorLabeling
---

Does anyone know how the color labelling is done in Mail.App..

The one that colors junk mail brow and other rule based events.
It has a rounded edge like the lables in Finder.

Any help is greatly appreciated.

/Jonas

----
Yes, there's nothing to it.  Override the drawRect method in your text cell and before calling super, get the width and height from the rect parameter passed in of your cell and stroke a straight bezier path with the round end caps style.  Apple uses two such paths, one which is two pixels wider and higher than the inner one, and several shades darker.

Wow, this forum is awsome!!  Thanks again..

