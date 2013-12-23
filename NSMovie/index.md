---
layout: page
title: NSMovie
---



An NSMovie is a wrapper for a QuickTime Movie, providing a simple interface for loading a movie into memory. The movie data can come from a URL or a pasteboard, including the drag-and-drop and cut-and-paste pasteboards. The data can be of any type recognized by QuickTime, including nonvideo data such as pure audio or even still images. Once loaded, you can obtain a pointer to the movie data and use the extensive QuickTime APIs to manipulate the data.

**Important**: NSMovie is obsolete and should not be used for new development. If you are developing for 10.4+, or 10.3 with QuickTime 7 installed, use QTMovie.

