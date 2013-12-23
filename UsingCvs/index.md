---
layout: page
title: UsingCvs
---



I would be interested in starting some discussion about using CVS with Mac OS X, especially with General/ProjectBuilder's built-in support for it.

One of the main things I am confused about is whether or not a General/NibFile has to be "wrapped" to be in a CVS repository.  I am not sure I understand what this wrapping entails, what I have to do to enable or disable it, and whether it is enabled or disabled by default.

I have enough CVS experience to basically understand what's going on, but I still haven't reached a level of "trust".  It still feels fragile to me, like if I do the wrong thing, I could trash my source.

General/CvsBasics anyone?

-- General/StevenFrank

----

I'd also be interested in seeing this, so I could figure out how CVS works. At the present time, I am as scared of CVS as Dustin is of Cocoa :)

-- General/RobRix

----

To get things started I added a few general pointers to General/CvsBasics. I might get around to experiment with CVS support in General/ProjectBuilder, too, but I won't be angry if someone else fills that part in first. :)

-- General/JensBaumeister

----

I'm not a CVS expert, but I'll see if I can add some more info.  Thanks for getting us started!

-- General/StevenFrank

----

Being someone who can't live (or code) without cvs, I quickly found out everything I could about PB CVS support. A few things:

It seems that you must first get a working directory of the source BEFORE opening PB or the SCM part of it will not know it's CVS source. Nib files are "wrapped" to prevent corruption by the CVS diff'ing and merging (as far as I can tell). I think Apple has modified the CVS source and added support for automagically wrapping the Nib. Otherwise, the folks over at General/CodeFab (http://www.codefab.com) have developed a modified cvs source distribution that adds the same kind of wrapping for Nib files (General/CodeFab does MOSXS and General/NeXT development mostly with General/WebObjects). I feel, from a CVS freak point of view, that the SCM part of PB is not strong enough for serious CVS use (no major support for branch or revision joins and arbitrary merges, and I don't like the fact that General/FileMerge is used externally although it's a great app). 

my two cents.

-- esammer

---- 

Thanks for the info.  Some of that is already mentioned in General/CvsBasics and General/CvsWrappers.  

One thing I'd like to know is if you have to do anything special with General/SourceForge hosted projects in terms of wrapping nibs.  You can't call out to wrapping commands on their CVS server, so I'm guessing that just indicating that files with .nib are binary should be enough?

-- General/StevenFrank
