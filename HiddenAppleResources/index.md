---
layout: page
title: HiddenAppleResources
---



I am trying to get a copy of the images that Spaces uses when switching spaces with the control+arrow key.  It displays a box and and an arrow.  I looked in the bundle for spaces but there are no images in it.  I also poked around in /System but couldn't find it.  

Is this being generated completely on the fly?  Glass-over effect on the spaces effect and all?

----

The arrow might be a font glyph (like how Windows pre-XP renders the minimize/maximize/close buttons).  Almost all glare and gloss type effects are drawn in code nowadays.

----

The bundle in the Applications folder is not actually Spaces. It's a fake application that links to the actual one (just like Expos�, Front Row, and Dashboard's). All of that is controlled by one application...the all powerful Dock.app bundle. It's in /System/Library/CoreServices and contains all of the resources for the above mentioned apps along with a lot more goodness. --LoganCollins

----
IANAL, but I'm not sure it's a great idea to actually copy resources out of other people's bundle's without permission. The images in Spaces, for example, are certainly copyrighted work. They're not part of any API or other developer resources, so it's not clear that Apple will be okay with you using them for your own ends. If you're bent on using them, you'll probably want to read them from wherever they live in the system rather than copying them into your own bundle and distributing them yourself, but I'm not sure that even that is safe. When in doubt, create your own or talk to your lawyer. -CS

----
Legally speaking it is perfectly fine to load those images at runtime, but it is a copyright violation to distribute them in your application without permission.
----
..and practically speaking, your app can break on 10.6 if you hardwire paths to images or whatever.

