---
layout: page
title: InsertingGutterIntoNSScrollView
---



I am trying to add an gutter view to an NSScrollView. I can draw the gutter in the main content of the scrollview, but when the scrollview gets bigger than the content view (and the vertical scrollbar becomes disabled) the gutter of course only draws in the content view of the scrollview, leaving a gap at the below the view and the bottom of the scrollview. I have tried to do something with NSClipView, but I don't fully understand much about it. I need the view to go from the top to the bottom of the visible rect of the scrollview.

- Jacob

----Check out NSRulerView. There is also some relevant discussion at LineNumbers.

----

Well, I actually checked out LineNumbers the other day. It doesn't help me, because i'm not using an NSTextView. LineNumbers moves the text over then draws the gutter in the view. I am using a custom view that is sized based on the content of the view, and and it starts out smaller than the scrollview, but soon becomes too big requiring the scrollbar.

- Jacob

----
Did you read past the first paragraph? There's a complete implementation of line numbering as implemented in an NSRulerView linked on that page.

