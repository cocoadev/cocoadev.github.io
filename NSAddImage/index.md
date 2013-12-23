---
layout: page
title: NSAddImage
---

http://developer.apple.com/documentation/DeveloperTools/Reference/MachOReference/Reference/chapter_1.2_section_2.html

"Adds the specified Mach-O image to the currently running process."

Use of this API seems to be discouraged. Apple's emphasis is on frameworks and bundles now, as opposed to flat dylibs and loadable plugins. If you want to dynamically load code into your application, use CF/NSBundle in conjunction with a code bundle.

----

