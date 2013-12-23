---
layout: page
title: GuitarChordView
---

Andrew Choi has posted some screenshots of a MIDI project that he's working on to his blog:

http://www.sixthhappiness.ca/blog/2004/05-09/index.html#6

The project contains a window with a view that holds text fields for the chords, as well as for beats of the song below them.

I'd like to figure out how to implement such a view for use in my little guitar/lyrics app. I currently have the lyrics displaying in the window with a simple string binding to an NSTextField (since I couldn't figure out NSTextView bindings). I'm using CoreData, which is the reason for using bindings. It's one string, with an invisible line break for each line. But each line must leave enough line in between for chords. And I'd like to maintain the use of those built-in NSTextField features, if possible. And I'm creating a custom model object for chords (a to-many rel to verses).

How would I best go about drawing this view? Subviews? NSMatrix? Something else?

----

Subviews and NSMatrix are not appropriate for this purpose. You'll have to write your own layout code in your view. Have you considered NSTokenField, where the chord names would be tokens?

