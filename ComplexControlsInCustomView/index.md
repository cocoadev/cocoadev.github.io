---
layout: page
title: ComplexControlsInCustomView
---

I have an application which must allow an arbitrary (but most likely large) number of complicated "control widgets" with labels to be dragged and positioned anywhere within the view. Obviously table views and matrices are out, since the size and positioning are 100% arbitrary.

The 'control widget' will need to have an editable label, a "marker" that can be dragged to reposition the whole widget, a clickable "x" button hovering beside it to remove it from the view, and an uneditable "short description" beneath the label. I'd like only a single click on the label itself to initiate label editing.

Looking at the Sketch app, I can see how a basic editable label could be done, but I'm confused as to how handle all this. Consider that the view could have hundreds (thousands?) of these "points of interest". I know I could achieve everything I want by making this widget a view with all the controls it needs, however this would be woefully inefficient compared to drawing the "pretty stuff" in a single view and just using cells and tracking rectangles for all the rest a la Sketch.

I'd like some opinions on the best approach to this problem. Any takers?

----

**Discussions**

I decided to take an approach more like that found in the oft-referenced Sketch example. I'm not sure if this is the **best** approach, but it's certainly the closest to what I want to accomplish. Using its approach toward only drawing the "graphics" that intersect with the rect the view is drawing is certainly more efficient, anyway. I also noticed that the Sketch example shows how to "swap in" an editor for their "text graphics" when it's being edited. So it seems the Sketch example is a near-perfect fit for everything I'm trying to do.

