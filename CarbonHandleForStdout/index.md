---
layout: page
title: CarbonHandleForStdout
---



How can I get an FSSpec for STDOUT in Carbon?

----

You can't, FSSpec<nowiki/>s only refer to items in the filesystem.

*Which means you could use     /dev/stdout but probably there may be an easier solution for whatever you're trying to do. --JediKnil*

----

How can I create an FSSpec for /dev/stdout? Using QuickTime's FSSpecFromNativePath does not work for /dev/ :(

A Handle (Carbon type) for STDOUT would work too.

I am trying to export a QT video to STDOUT rather than a file. Any clues? :/

----

I dunno, maybe you could try asking on a *Carbon* web site. Somehow I don't think you'll get the best answers on a site named *Cocoa*Dev

----

I would, if I knew of such a site. Besides, the rest of the code *IS* Cocoa, its just that Cocoa does not cover all of QT's API.

All I need is a Handle or FSSpec for STDOUT, surely it can't be that difficult in Carbon. Or if it is, does anyone have an example for doing a lower level conversion using QuickTIme, where you get the data for each frame and can write it to disk yourself?

----

Not knowing of such a site is not an excuse for asking on the wrong one, there is such thing as Google. As a free hint, http://www.carbondev.com/ .  "The rest of the code is Cocoa" is extremely dumb; how does the rest of your program have any bearing on this particular thing? As to the last question, have you checked Apple's sample code? For when you re-ask this question in a more appropriate forum, may I suggest that you cut right to the part about getting raw exported data from QuickTime? Asking weird questions about getting an FSSpec to stdout, and then only later revealing your true purpose, is not a good way to get help.

----
You'd seem a lot smarter if you answered my question. But thanks for the hint, anyway.

----

If I were smarter then maybe I'd know. But I don't. This whole discussion about "maybe you'd get better answers" isn't so I can be a jerk and show off how dumb you were to ask your question here. I seriously do not know the answer, and I seriously do not expect you to get a useful answer here, because it's vastly off the topic of the site. Since this is ultimately a QuickTime problem, Apple's QuickTime mailing list is obviously a *much* better place to ask.

But, for whatever reason, most people take "this is the wrong place for this question" to be either an insult, a way to show off and avoid giving the answer that the person knows anyway, or both.
----

There's **no way** to "get an FSSpec for STDOUT" using Carbon or any other technology.  The Standard C Library on Mac OS X is not implemented on top of Carbon, it's implemented quite a bit underneath it.  Furthermore, neither an FSSpec nor a Handle represents a *file handle* - a file handle (properly called a *file descriptor* on Unix-derived operating systems) is an integer returned by the kernel from an open(2) system call or something implemented atop it.

The best thing you can do when asking questions is to articulate your end goal, and not the process you expect to use to get there.  Often developers who are new to Mac OS X have serious misconceptions (as above) about the implementation of the things they're trying to use; if you ask questions based solely on your expected implementation, there's a good chance you won't be able to get the help you need to actually achieve your goal.  If instead you ask questions about your goal itself, you're much more likely to get the help you need.

In this case, what I bet you need to do is write a custom QuickTime data handler that writes to an open file descriptor that you specify.  Then you can pass this data handler to whatever QuickTime export mechanism you're using and your code will be called.  This will let you use whatever mechanism is appropriate for your code to actually output the data once QuickTime has it ready for you.

Note what I did above:  I took the *actual goal* you described well after asking your original question, and described a way to achieve that goal.  Your original question would not have let me do that.  Neither would any of your insults towards those who were trying to elicit more information on what you were actually trying to achieve *so they could help you.*

*If nothing else works, you could still write to a temp file, then print the file to stdout. Sorry about the     /dev/stdout hint; I didn't actually try it before I said it. --JediKnil*

