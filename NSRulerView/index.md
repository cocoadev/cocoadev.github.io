---
layout: page
title: NSRulerView
---

NSRulerView is designed for displaying ruler lines alongside the document view in a scroll view. NSTextView uses NSRulerView to display the tab widths etc, but it can be used for variables grids in a graphics application as well.

NSRulerView is not *just* for the standard "ruler" however. For example, line numbers, error/warning icons, breakpoints, and the code folding ribbon in the Xcode text editor is all done in an NSRulerView subclass. An example of that is the NoodleLineNumberView in [https://github.com/MrNoodle/NoodleKit NoodleKit] along with the a more recently updated version: https://github.com/scottharwell/NoodleKit. See the LineNumbers page for discussion and links.

