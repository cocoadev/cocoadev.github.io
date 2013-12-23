---
layout: page
title: OpenWithList
---

Is there any easy way to get access to the list of Applications that the Finder lists on "Open With..."?

If there's no easy way to do that, how can I get a list of Applications that can read a certain file? I know that I can use     _LSCopyAllApplicationURLs, and then test each one with     LSCanURLAcceptURL, but     LSCanURLAcceptURL returns Applications that accept everything (things that have ****** in there     CFBundleTypeExtensions part of their Info.plist), so I get unrelated results like Mail.app, stuff in CoreServices, et cetera. Would the easiest way be to try to parse the Application's Info.plist myself, or is there a LaunchServices function that I've overlooked?

