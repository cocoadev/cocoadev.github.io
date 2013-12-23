---
layout: page
title: InteractiveShellCommandFromProgram
---

I'd like to launch a shell command from my program, but this command may need user input (i.e. be interactive like ssh, CVS, /usr/bin/sh or whatever).

Is there anyone who can point me toward what I need to know for this setup? I realise it might be complex, allocating tty-devices, terminal emulation and such -- but I am ready to do it all (basically a full terminal emulation), I just have not been able to find any canonical information guide to which steps one must take, and since shell/terminals have like 30 years of history with a lot of legacy protocols, it's really a rather diverse subject with dozen of blind ends.

So if anyone could perhaps help me with some guidance? maybe in the form of links. And please do not just say that I can take a look at Terminal.app from GNUStep or Emacs terminal emulation -- I really want to understand these things, not have to read through thousand of source code lines...

Thanks in advance!

----

Look at the Animal example at http://www.cocoaprogramming.net (downloads section I assume).

Or take a look at expect: http://expect.nist.gov/

----

Thanks for the links, but what I am after is a description of curses, ncurses, tty and all the other components involved in a unix terminal session -- the Animal example just run a task and communicate via stdin/stdout, which is not sufficient for more advaced programs (like pico), and I am not sure what to do with the Expect reference, this is a giant framework for scripting terminal sessions, albeit the author of this framework probably could answer most of my questions, I doubt he'd be willing to, and as I stated above, I'd hoped the answer would not have me resort to reading some other persons source code to find the info I am after...

----

Learning enough to build a terminal emulator is a far larger job than learning something like expect.

Some books on the subject:

http://www.oreilly.com/catalog/curses/
http://www.oreilly.com/catalog/term/

And an article: http://www.onlamp.com/pub/a/onlamp/2001/03/22/tty.html

Here's a link to code (Objective-C code even) http://sourceforge.net/projects/iterm/

Good luck.

