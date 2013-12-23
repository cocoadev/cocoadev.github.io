---
layout: page
title: ColinTA
---



I'm Colin.  I'm new to Objective-C and to Macs in general.  I was never attracted to the Apple Macintosh until they released X, which I truly believe is a wonderous OS.

March 11, 2006.
Quick note: I'm no longer working on the File Viewer project. It was too much work, and Tiger has done a great job of things (though I still don't like using just one Finder window).  These days I'm working on a spreadsheet program that is both like other programs and unlike other programs.  It's how a spreadsheet program should look on a Mac.

2003
I'm working on a Finder replacement (File Viewer until I think of a better name) that is based on the ideas of John Siracusa in his article about the "Spatial Finder."  http://arstechnica.com/paedia/f/finder/finder-1.html

My concept is this: There's a central app (FVApp) that loads the plugins and handles preferences.  A plugin can talk back and forth to FVApp to find out what kind of plugins exist, what the preferences are for its path, etc.  If you can't see what could be done with such a system consider this: Open your home folder and you have your pictures/documents/music folders sitting there.  When you open them, another folder that looks just like the home folder opens up, either in a new window or the same window.  But if you want to look at pictures, why doesn't a specially made window popup?  One that can view pictures, not just thumbnails.  A music folder could look like iTunes (and use applescript to interface with iTunes, if so desired).

I'm planning a major object, the FVFle : NSCell, that will handle file viewing, and an accompanying FVFileView that will handle robust sorting, viewing options of said files.  I've just begun, it's my first project, and it's gonna take me all summer to make much progress at all.  It's open source, though, and I hope that it will be worth people's time to check out and tweak.  I also hope that Panther doesn't make it totally useless.

*Welcome, and hopefully we at CocoaDev will be able to help you out. -- KritTer*

