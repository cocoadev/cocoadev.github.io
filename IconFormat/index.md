---
layout: page
title: IconFormat
---

So, I'm trying to learn cocoa, and I thought a good little project would be to make an app that would play with icons.  The problem is I can't find any information or documentation on the icon format.  Could someone point me in the right direction?  Thanks.

----

Try IconFamily by Troy Stephens. It's a very nice Cocoa wrapper around the icon apis.

----

Yea, IconFamily by TroyStephens is awesome. Also note that icns file format chills as a type of representation in NSImage. You can check out the source of Iconveter for an example of using NSImage, Troy's class, and DavidRemahl ' s icon2ico class to manipulate icons. It can be found here: http://www.versiontracker.com/redir.fcgi/kind=4&db=macosx&id=16468/iconverter-source-085.tgz

