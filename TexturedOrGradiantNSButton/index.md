---
layout: page
title: TexturedOrGradiantNSButton
---

I am trying to create a button with a similar look and feel to the iPhone button. Is there an easy way to create a General/NSButton with the blue type of gradients that something like the keyboard has? Can we specify the gradient or texture a button is suppose to use as its backdrop. I originally tried just taking an image and setting it as the icon, but then I got the button border around it still and if I removed the border I lost the nice roundness.

Thanks

----

No, but you can subclass General/NSButtonCell and handle the drawing yourself. There are examples on this wiki showing how to create gradients (of any color) as well as how to created the rounded rectangle look. Use the site's search feature to find them. It's not terribly difficult if you're familiar with drawing in Cocoa. If you read the documentation on controls/cells, you'll see exactly what methods to override.
