---
layout: page
title: TagCloud
---

A General/TagCloud is a way of displaying a set of terms that allows for perusal alphabetically and by frequency. See http://en.wikipedia.org/wiki/Tag_cloud.

A Cocoa implementation is available at http://www.fernlightning.com/doku.php?id=randd:tcloud:start.

----From Jerry Krinock:  This is very cool.  I have several enhancements to this project:

- if enclosed in scroll view, scrolls to show all tags.
- if no scroll view, only shows tags that fully fit in the view
- extend/multiple selection by shift-clicking or cmd-clicking
- selections remain highlighted until deselected
- added setEnabled:(BOOL)yn  (no = cannot select items)
- added a KVO-compliant read-only getter for "selected tags"
- argument to -setTags: (formerly setStats:, I believe) may now be
   General/NSSet, as well as an General/NSArray, and it may contain simple
   General/NSString objects as well ofCountedTag objects.
   In the latter case, all tags are drawn with same size.
   The argument to -setTags may also be nil, in which case the
   layout will be skipped, and view will be empty
- fixed memory leak (General/NSScanner, scan)
- Re-cast as an General/NSControl which sends an action on leftMouseDown
- Instead of setStyle:, there are two setters:
     -setShowsReflections:(BOOL)yn
     _setBackgroundWhiteness:(float)whiteness
        (The black is a little too extreme for a non-game app ;)
- Becomes first responder and responds to selectAll: by
   selecting all tags (not working yet)
- Demo app shows most of the new features, such as a tableView
   bound to selectedTags

I will make it available when done testing, or let me know if you want it "as is".  jerry@ieee.org  ----


----

http://www.sheepsystems.com/sourceCode/sourceRPTokenControl.html
