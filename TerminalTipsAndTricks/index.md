---
layout: page
title: TerminalTipsAndTricks
---

To reveal the current working directory in the Finder, type     open .

open is very practical,     open <file> will simulate a doubleclick, but one can also do e.g.     open http://apple.com  to open that page (in the default browser), and one can also specify the application to use, e.g.     open -a VLC someMovie.avi to open the movie with VLC.

pbpaste will dump the contents of the pasteboad and pbcopy will read from stdin and place that on the pasteboard.

Unfortunately the shell starts all commands in a pipe-sequence simultaneously so     pbpaste | sed ... | pbcopy will fail, but one can delay the pbcopy like this     pbpaste | expand -3 >/tmp/crap && pbcopy </tmp/crap.

Or, if you do it a lot, in tcsh create an alias like
    
alias pbfilter 'pbpaste | \!* >/tmp/crap && pbcopy </tmp/crap'

or in bash, a function a la:
    
pbfilter() { pbpaste | $* >/tmp/crap; pbcopy </tmp/crap; }


Cmd-Ctrl-V pastes escaped text from the pasteboard.

Cmd-Shift-V pastes the current selection (w/o placing it on the pasteboard).
