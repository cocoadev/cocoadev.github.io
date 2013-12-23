---
layout: page
title: GenieFlashEffect
---

Hi,

For anyone who has used the private CGS Framework (http://dev.lipidity.com/apple/the-ultimate-core-graphics-resource) or done the Genie Affect on there own, does anyone know why there is a brief flash of white just when it finishes the Genie minimize. I get this on the sample application as well. I cannot see anything that would be causing this.

Thanks

----

No idea if this is your problem, but I've seen similar things happen when using OpenGL in a window that's marked as One Shot. Undoing One Shot stops any flashing.


----

Hey,

thanks for the help, unfortunately that did not fix the issue. I am not dealing with OpenGL either, just NSWindows, NSViews, NSImages, and NSImageBitmapRef's.

