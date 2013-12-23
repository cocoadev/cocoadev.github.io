---
layout: page
title: MetalSlider
---

Is there any available class that duplicates the metallic looking slider found in say, iTunes?

*Not that I'm aware of, at least not publically and supported, but you could see if ProKit has anything to offer.*

**Well I was faced with the same dilemma. I ended up creating my own from scratch with an NSView, writing all the mouseDown, etc. code and creating my own track and knob graphics. Since this was for a special kind of audio playhead that needed tickmarks inside the track itself to serve as a 'bookmark' mechanism of sorts, the code was significantly more difficult, but the actual slider control itself was relatively easy to create. Check into NSView, NSImage, and drawing in Cocoa. Everything you need to know is there in the docs.**

This sounds pretty cool, can you post the code, either here or as a link?

**Well, yeah, but it'd take me as much time to isolate and post it as it would take you to read about NSViews. You're half there by then anyway. Seriously - it will do you no good to have someone do this for you. This site is great and has many pieces of the same code - just look, read, and learn. You'll thank yourself for it later.**

----

Well, if you did through Apple' app bundles, iMovie, iPhoto, and QuickTime all have images for custom sliders in there bundles. They are subclassing NSSlider, and drawing the control using the custom images.

----

I'm the OP thanks for all the responses, I'm going to look into the custom subclass.

*I'd subclass NSSliderCell and override     drawKnob: and     drawBar:flipped: but if you want to take the long way...*

