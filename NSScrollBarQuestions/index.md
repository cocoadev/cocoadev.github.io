---
layout: page
title: NSScrollBarQuestions
---



I have two views which need to be controlled from one scrollbar... how do you do this?  my initial experimentings have not been successful.
the basic idea is that I have a custom control which is a really big matrix. above this custom matrix control is another view (which is optional) and shows additional information about the columns of the matrix.  when I scroll the matrix I want the additional view above it to scroll in sync with the matrix.  having two scrollbars is too confusing, i just need one scrollbar to control multiple scroll-views.  what I have tried is looks like this in pseudo-code...

[myAdditionalControl superview] superview] setHorizontalScroller:[[[mainController superview] superview] horizontalScroller;

which makes a big screen-fart.

----
There's actually a quite easy way to do this, as long as you use two scroll views instead of one. In InterfaceBuilder, connect or modify a controller class, with outlets connected to each scroll view (yes, this is possible...just drag the connection line to the scroll bar). Then, in the     awakeFromNib method, bind the two scrollers together like this:
    
firstScrollView verticalScroller] bind:@"doubleValue" toObject:[secondScrollView verticalScroller] withKeyPath:@"doubleValue" options:nil];
// You may have to do this again with the two scrollers reversed.

And that should do it! --[[JediKnil

----
thanks alot JediKnil, but unfortunately this doesn't work either.  it does demonstrate some valuable voodoo knowledge (thanks for that!), but it unfortunately doesn't do anything at all (I checked that my controller variables are getting connected correctly and they seem to be valid).  any ideas?  |K<

----
Trying to use one NSScroller instance in two different scroll views at the same time is unlikely to work.  NSScroller is an NSView subclass, and each instance on NSView can be in zero or one view hierarchy but never two.  Read up on the view hierarchy to answer any questions.

The easiest way to get what you want if you don't mind having your column info view vertically scroll out of view is to just make both of your views subviews of the scroll view's document view.  Then they will always scroll together as their parent view scrolls.

It sounds like what you want is similar to NSRulerView and the way rulers track the content scrolled in a scroll view.  

If you want ruler like behavior in which your column info view scrolls horizontally but never vertically, you have two options:
- You can subclass NSScrollView and use -tile to perform your custom view tiling within the scroll view or
- You can use your column info view as a ruler.
+ setRulerViewClass:
� setHorizontalRulerView:

----
Yeah, after I had posted my question I began exploring the ruler implementations.  I need to make sure that they'll do what I need:  because I definitely can't accept having my column info view scrolling out of sight...  but in this column info view, per column will be buttons, knobs, and sliders as well as an animated display and label...  maybe I can register each of these controls as a different ruler "unit" which would be great...  but I'm definitley still exploring and open for suggestions!  Thanks alot!  |K<

----
after just a few hours I do have some really great ruler views happening!  thanks to Koen van der Drift for the example code!  
the only frustrating thing about the rulers is that with the horizontal rulers the dynamic redraw when one is scrolling horizontally looks totally crappy. is there a way to fix this?  

QUESTION:  does anyone have example code on how to use the accessoryView feature of the ruler class?

thanks!  |K<

