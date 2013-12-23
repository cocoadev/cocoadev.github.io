---
layout: page
title: FastCollisionTestingTechniques
---

Alright, basically I am using a WebView with a bunch of divs.  Now, when I hover any area, I want to be able to know exactly what elements are under the mouse pointer.  Originally I thought I could do this just using javascript: simply stick a onmousemove handler on every div, and when the mouse hovers over, it becomes "active".  However, i ran into the complication of overlapping divs.  Since the events don't propogate downwards, I only find out about the topmost element if there are overlapping elements.  The question is, is there any good technique to resolve this issue without having to resort to scrolling through dimensions to figure out if the pointer intersects any divs.  If I do have to do this, then I'm probably going to use the WebScripting object to speed it up by keeping a log of div dimensions in NSRects and doing a bunch of NSPointInRects.  I'd really love to know if there is something that I'm just missing to avoid this sticky situation, because speed is really an issure.

Thanks in advance!

- FranciscoTolmasky

