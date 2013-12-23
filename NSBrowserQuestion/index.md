---
layout: page
title: NSBrowserQuestion
---

I have pored over the NSBrowser docs and I'm confused as to how to go about doing something like this.

I have a hierarchical data structure like this:

    -object 1
      -object 2
      -object 3
           -object 4
-object 5
      -object 6
      -object 7
      -object 8
           -object 9
           -object 10
                  -object 11

When I put this data in an NSOutlineView, I loop through it with a recursive method populating some custom 'outline node' cobjects, then I set the rootnode to the main item of the outline, and reload the data. But for an NSBrowser, there is seemingly no way to just feed it a 'root' item and then let it display everything. Advice?

----

Look at the example at file:///Developer/Examples/AppKit/SimpleBrowser

----

I'm looking for a simple way to make an NSBrowser show the content of only one folder.
The documentation on NSBrowser only claims its methods and properties, I haven't found anything useful on how to make it display some real content.
All I need is this: an NSBrowser that displays the files inside a folder located at /Applications/Web Dev/Functions/ as a list and then when you click on any of the files it will load the file's content and insert it on a TextView.
I just need some help on the "Show Folders Contents" thing...

----

Check out the SimpleBrowser project in /Developer/Examples/AppKit.  It's a straightforward example of how to show a file hierarchy in an NSBrowser  -- Bo

