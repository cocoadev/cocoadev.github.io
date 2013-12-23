---
layout: page
title: DisplayList
---



A DisplayList is an OpenGL entity which caches drawing instructions for later use. It is perhaps the most basic optimization available in OpenGL, and it's also very effective; Apple's put a lot of work into making sure that display lists are speedy on MacOSX.

**Discussion**

I am making a fairly simple screensaver where a few images are stretched, but I find the speed of the animation slows down a lot when the stretching gets big... what is the best approach to keep the animation speed consistent?

----

This has nothing to do with screensavers. They use the same animation techniques as anything else. Look for info about animation for games, etc., and it will all carry over.

----

Well, I've done animation before, but here its just not working out. I am using OpenGL now and still am getting this slowdown, its faster then slower, faster and slower.... repeatedly. I've tried using a timer to do the calculations outside of the animateOneFrame but it hardly makes a difference. Any pointers, anything?

----

Actually, I don't thing basic math calculations can be the cause of the slow down. Are you conducting your tests with other apps running in background ? I think you could get more interesting results using caching techniques, but I'm not advenced enough in this domain to help you very much...

----

I am loading the textures onto the card, then I am using a DisplayList to render. I just can't seem to find a way to get a consistent, smooth flowing frame rate :-/ Its always a high one, but its just not consistent and the change is noticeable. 

----

Is the problem that the frame rate becomes too low when it varies, or is the problem that your object's speed changes? If it's the latter, that just means that you need to make your animation use the current time to determine its position, rather than changing a constant amount per frame.

----

In general to get a constant animation speed you will need to decouple your animation from the framerate, this means you cannot do your animationstep every frame, you have to measure how long the time was between the last and this frame and then scale everything accordingly, i.e. scale, size whatevery you are doing to animate. I think this is what the last person meant.

----

*Yes, that's what I meant. Scale and move things based on time, not on the frame count.*

