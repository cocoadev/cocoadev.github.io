---
layout: page
title: AGRegex
---

http://sourceforge.net/projects/agkit

A Perl-compatible regular expression framework using the PCRE library.

----

Does anyone know if it is x86 compatible ? i mean, can i just click the 2 magic checkboxes to get a universal binary ?

----

I just recompiled it as a Universal Binary. I had to update the targets first, and then clicked the "magic checkboxes" and it did the trick. --Daniel

----

Thanks Daniel

----
The problem with this framework is that it uses a (very) old version of PCRE (v. 4.3), which doesn't include most of the newer matching operators.

----
I succesfully compiled AGRegex with PCRE 6.7 (June 2006). I have a xcode project under svn at http://OpenSVN.csie.org/cocoa/RegexTremendae/. Look at the AGRegex target which is an embedable framework.  

----

I'm very interested by having AGRegex updated. but i can't access your openSVN because it requires a login/pass... i've trie anonymous/anonymous with no success. Help ?
----
Yes... PLEASE provide some sort of anonymous login for this.... we need this! -Seb
----

just a bump, sorry, i really want this guy to open his svn ...
----
I tried to get into contact with the owner of the repository, but the site's admin informed me all the account's emails bounce. -Seb
----
    :(
----

it seems the link now works!  he must have come back form his long vacation... thanks.

----
Hi all, sorry for the delay. I moved my repository to googlecode because of the poor reliability of openSVN. Check it there anonymously: http://cacaodev.googlecode.com/svn/trunk/RegexTremendae/ . Btw, it's an Automator action for regex.

What I did is just update pcre to 6.7 and recompile AGRegex, that's all ! If you want an official upgrade of AGRegex, I think you should contact Aram Greenman, the original author.
Regards, Romain.
----

Thanks man! I did'nt really test it (performance wise), but anyway the old AGRegex was using PCRE 4.3 !

----

RegexTremendae seems to be an Automator regex action, which uses AGRegex but doesn't package it for use by other applications. It's also entirely undocumented. Those looking for a universal, updated version of AGRegex that doesn't force a change of API should check out Colloquy's AGRegex fork. It appears to be universal and uses PCRE 6.7.

http://colloquy.info/project/browser/trunk/Frameworks/AGRegex

SVN checkout instructions are here: http://colloquy.info/project/wiki/Source%20Code

You can, of course, just check out the AGRegex project by specifying its path to "svn checkout".

----

It should be noted that Apple uses AGRegex in ARD extensively. Just an FYI.
-- W

