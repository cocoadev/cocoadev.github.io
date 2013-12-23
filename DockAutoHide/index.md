---
layout: page
title: DockAutoHide
---

If you have a full screen application and you don't want the dock to get in the way (such as iMovie 2), set the LSUIPresentationMode in your info.plist to 1 as is below:

    	<key>LSUIPresentationMode</key>
	<integer>1</integer>

If you wish to remove the MenuBar also (but still have them available if you if you mouse near them, set the value to 4).

Source: http://developer.apple.com/documentation/MacOSX/Conceptual/BPRuntimeConfig/Concepts/PListKeys.html

----

