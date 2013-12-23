---
layout: page
title: CocoaNIBDesignAdviceForADummie
---



http://www.koen.nu/upload/Question.jpg

----
I would do it with one nib and just use a tabless tabview for your main view with 3 different tabs for each of the possible views in the main view.

----
That becomes unmaintainable in no time at all. One nib for each view.

----
I would put each of your custom views in the main nib (not attached to any window) and make an IBOutlet in your controller for each. Skip the tab view since that's just a pain for stuff like this (IMO), and load each view by making it a documentView or subview of the scrollview, splitview, or whatever you're using as a container. If you want, it's also pretty easy to resize the main window whenever the view changed by doing it this way.

Creating a new nib for a single NSView is just overkill, and in my mind a lot less maintainable than anything else.

