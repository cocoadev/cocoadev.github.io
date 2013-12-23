---
layout: page
title: TrackingAMovingWindow
---

Tracking a moving window is tough as notifications are not send during the dragging but only just prior to start and upon ending (or pausing). This is oftentimes enough, but sometimes it just is not. Particularly uncomfortable is the fact that a dragged window does not respond to [window frame] as the General/WindowServer manages dragging and "freezes" the window's frame rect accessors. 

A possible solution to that problem is implemented in my sample "T<nowiki/>rackingAWindowFrame" ( http://www.sb-software.de/marco/dev/General/TrackingAWindowFrame.zip ) and briefly outlined on my blog post: http://web.mac.com/mabi99/iWeb/marcocoa/blog/E24D8A16-197F-40B5-92F1-70253CD57806.html

The sample project compiles to an application with two windows, a "compass" and a "magnet", with the compass needle always pointing towards the magnet, no matter which windows is moved or resized. The exact coordinates and dimensions of the magnet window are also written to text fields in the compass window and continuosly updated during dragging or resizing:

http://www.sb-software.de/marco/dev/General/TrackingAWindowFrame.jpg



--General/MarCocoa
