---
layout: page
title: CustomControlsWithAquaLook
---

I'm wanting to create some new widget types that have the correct Aqua look (including being responsive to user preferences for tinting, etc.) As far as I can tell, the only "Theme" information that I can find is the Carbon Theme Manager API, which is someone limited for my purposes (doesn't give access to the actual brush patterns or gradients for example). I've tried calling the theme manager from my Cocoa application, but I haven't managed to get it to work, I suspect that it might be due to the fact that Carbon uses a different coordinate system?

Here's one example of what I would like to do (there are others):

Specifically, what I am working on is an application that shows a series of horizontal timelines, each with in a seperate splitter pane. Each split panel has it's own vertical scrollbar, while there is a common horizontal scrollbar for the whole set of panes. (Look at any MIDI sequencer for an example of this sort of UI). Typically in popular applications of this type (on other platforms and in previous versions of Mac OS), there are "zoom" buttons in the window border, in line with and next to the scrollbar arrows. The horizontal scrollbar has a pair of buttons to control the horizontal zoom, and the vertical scrollbar(s) each have a pair of zoom buttons to control the vertical zoom for that split panel.

I can certainly put a button with a plain square bezel in the border, but it looks odd because it doesn't match the look of the scrollbar arrows.

-- Talin

[Talin, I personally think this is a Good Thing(tm). Since the zoom button isn't really part of the scrollbar, it should look different. As to why the Appearance Manager's themes don't work the way you expect they were written to be used from a Quickdraw GrafPort. Until now, Apple haven't exposed the internal CoreGraphics implementation of these. -- UliKusterer]

----

Similarly, does anyone know the algorithm for doing the gelatenous aqua-style blobules?  I know I can use Stick Software's Aqua Tint, but I'd like to be able to do some of this at run-time

----

I am afraid that there is no help in the system with regard to the Aqua look. All system controls are composed of small bitmaps and these even change from release to release.

With respect to getting the tint (blue or graphite) then there is new Cocoa API for this under Panther.

And yes, it really sucks... Apple doesn't even provide a way for us to use their "standard" icons like the lock, the brighter pinstripe texture (as used behind status bars in Apple applications) and previously to Panther also the question mark, the disclosure triangle and so on...

----

OK thanks for the answers. I kind of suspected this would be the case. What I think I will do is try to come up with a look that is "artistically compatible" with the different themes.

----

What I ended up doing was subclassing NSButton and then overriding *a lot* of its methods to get the background of a button, but with the behavior of my widget.  Maybe that would work for you too?

