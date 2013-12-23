---
layout: page
title: BeOS
---

**BeOS** - *A personal computer operating system developed by Be Incorporated in 1990 as a media OS.*
----

http://macspeedzone.com/archive/art/con/images/bebox.gif

The C++ twin of OPENSTEP?

In a sense.  BeOS, like NeXT, was formed by some Apple refugees, wanting to throw away the LegacyJunk of MacOS and start afresh with a new OS.  Unlike NeXT, they didn't base their OS on an established player (BSD unix), but instead started from scratch.  BeOS was unique in that it was very heavily threaded.  Every window was its own thread (actually two threads, one in the application, one in the window server), which led to very very responsive programs.  So responsive that the OS didn't supply a wait cursor.  There were some graphics demos on the original beBox (dual 60 mhz PPC I think) that are just now starting to run on OS X with much more powerful processors and graphics cards (like the demo of 6 different quicktime movies playing on the sides of a rotating cube, and the machine was still very responsive to other more mundane user tasks as time went on).

The user-space toolkit was in C++ (one of the nicer C++ UI toolkits I've seen), and kernel-space stuff was all in C.  BeOS had some really neat features.  The filesystem acted like a database (originally it was a database, but performance wasn't good enough, so they made a somewhat more traditional filesytem with db-style add ons.  The file system was journaled - many, many years before it became fashionable in the Linux and Mac world.  You could attach arbitrary metadata to files, and do system-wide queries on that meta data.  Like mail messages.  The contents of the mail message is in the file, and things like the To:, From:, and Subject: headers are also in the metadata, so you could use the Finder equivalent to manage your mailbox.  The lead dude on this, Dominic Giampaolo (who now works for Apple.  woo hoo!) has written a book describing the guts of the Be file system (blurb at [http://hallcomputer.com/operating_systems/14.shtml]) .  It's fascinating reading, even if you're not really into OS guts.  ++MarkDalrymple
----

The metadata master, D. Giampaolo now works for Spotlight.  Indeed, it was long expected in the rumor sites before Tiger came out
that OS X would incorporate some kind of extension based on metadata when we knew that he was now hired by Apple.
The book on the file system can be obtained (for free!) from his webpage.
http://www.theregister.co.uk/2002/03/29/windows_on_a_database_sliced/ contains an interesting background in the history of BeOS, and the future of WinFS :)

----
**See Also:** 

*http://en.wikipedia.org/wiki/BeOS
*http://toastytech.com/guis/b5pe.html
*http://www.nobius.org/~dbg/   Dominic Giampaolo's homepage.

----

Apparently a lot of the BeOS alumni now work at Apple.

----

Pity it was a failed attempt. :( They coulda started the revolution of getting away from Windoze.

----

Pity Microsoft used its monopolistic powers to crush them into non-existence.

----
**Sigh**

----

I used to do all my programming on BeOS... those were the days... I spent a couple years doing Qt programming on Linux and, well, it wasn't until I discovered the joy of Cocoa that I felt like hacking was fun again.

BeOS was magical.  When a friend brought his BeBox to work, our collective jaws dropped.  It was so fast,  so elegant, and so geeky.  It's a shame it never took off.  ++MarkDalrymple

BeOS IS magical... just bought a laptop to relive the olde dayes once in a hwhile. It's Lovely(tm)...

----

On a related note,

(www.beincorporated.com)
Be will receive a payment from Microsoft, after attorney's fees, in the amount of $23,250,000 (U.S.) to end further litigation, and Microsoft **admits** no wrongdoing.

-- Xypher

----

Check www.haiku-os.org for an open source creation which aims to be binary compatible with Be R5 for its first release. As of Oct 2006, it looks like that could be done within a year.

Also, BeOS R5 itself can be downloaded from www.bebits.com.

----

It runs in Parallels too :)

