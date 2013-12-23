---
layout: page
title: CocoaScheme
---

This is a very preliminary page, for people who will be working on integrating Emacs/Scheme with MacOSX/Cocoa. I have no clue if this will take off. 

Quick notes on getting started with MIT-Scheme (on other platforms): 

MIT Scheme is at: http://www.gnu.org/software/mit-scheme/

Scmutils (which makes MIT-Scheme useful) is at: http://swiss.csail.mit.edu/classes/symbolic/spring06/software.ssp

Useful to also have paredit: http://mumble.net/~campbell/emacs/

To run MIT-Scheme from Emacs, add the following to your .emacs file: 

(load "xscheme.el")

(setq scheme-program-name "/usr/local/scmutils/mit-scheme/bin/scheme")

(setq scheme-program-arguments "-large -heap 6000 -emacs -library /usr/local/scmutils/mit-scheme/lib")

Restart emacs, and to start Scheme, run m-x run-scheme

To execute a command, c-x c-e. To executa a block, m-z.

