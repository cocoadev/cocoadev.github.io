---
layout: page
title: OpeningBundlesWithNSOpenPanel
---

CocoaDiscussions  - How do I open a bundle with NSOpenPanel
I'd like to navigate into application bundles with NSOpenPanel. I've seen it done in Resourcerer, but I can't find any information on how to do it.

*Take a look at -setTreatsFilePackagesAsDirectories:, a method on NSSavePanel, which is NSOpenPanel's superclass. When browsing docs, don't forget to look at superclasses!*

