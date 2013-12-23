---
layout: page
title: SegmentedInspector
---

I am eager to build a segmented inspector palette for my app, as seen in a lot of the Omni products like OmniGraffle, where sections of the inspector can be unfurled or hidden depending on what you desire to see. (Also seen in the finder when you Command-i on any file or folder).

Does anyone have any pointers or tutorials to approach this problem?

(I *know* it was something that was covered in one of the sessions at WWDC 2003, but I only attended this years, and as expected the same tips and tricks were not covered as last year).

----

To get something like Finder's get info palette, basically you could to set up the panel in Interface Builder with all the disclosure triangles open. At startup you close the appropriate disclosure triangles, hide the views in the closed triangles, and resize the window. Don't forget to move the disclosure triangles and lines around when resizing the window though. To make the window animate when opening and closing the disclosure triangles you can use     [window setFrame:newFrame display:YES animate:YES]. An alternative method might be to set up a custom view for each section in IB and set up the panel with all the triangles closed. You could then add the custom views to the panel when the disclosure triangles are opened. OmniGraffle does something much more complicated and I can't help you there. You may want to do a search at http://www.cocoabuilder.com for some more info. -- RyanBates

----

MOKit has a MOViewListView that provides exactly this.

