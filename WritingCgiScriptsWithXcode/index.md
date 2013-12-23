---
layout: page
title: WritingCgiScriptsWithXcode
---



I'd like to try my hand at writing CGI scripts with Xcode, if it's possible. I'd prefer to do it in C, but I can do it in C++ if I have to. However, the web server I'm intending to use the scripts on runs Linux (Red Hat 7.3). It has cgi-bin support, but I'm doubtful that Xcode can compile progs that can run on Linux. If it can, that's great - could somebody point me to examples of how to write General/CGIs with Xcode?

--General/OwenYamauchi

Ugh... I used to write General/CGIs in C, until I discovered General/PHPLang. C isn't made for that sort of thing so you end up having to escape a whole lot of characters, find/write some routines for parsing the arguments and stuff, all while dealing with the usual inadequacies of C strings etc, so it can be pretty tedious. But what I did back in those days was write the code using General/XCode (or Project Builder, Codewarrior, whatever I was using at the time) so I'd have all the nice syntax highlighting etc, and then I uploaded it to the server and compiled it from the server's command line with something like 'gcc -o blah blah.c' But really, I recommend using something which is more suited to web stuff, like PHP for example. General/XCode can do all the right syntax highlighting for PHP (I think it used to need a plugin for that, though it doesn't seem to any more) and then that can be uploaded to the server and no compilation is necessary, due to the fact that it is in fact a 'script' rather than a compiled program. What's more, you can easily test it locally in a web browser (which I never figured out how to do with C General/CGIs), just enable PHP by uncommenting references to it in httpd.conf (there are lots of sites which say how to do this, http://www.phpmac.com/articles.php?view=136 is the first one I found just then), (re)start web sharing, put the PHP file in your Sites directory, and access it from http://localhost/~yourusernamehere/somephpfile.php --General/AngelaBrett

*(which I never figured out how to do with C General/CGIs)*  a cgi is a cgi. just setting the executable bit should have been enough.

----

You should only write General/CGIs in C if you're being paid lots and lots of money to do it. Try Ruby or Python instead. As an extra bonus, both of these languages have Cocoa bridges, so you can write apps in them too, if you want.

And if you do use C, don't bother with General/XCode. It's just not made for the job; just use a plain old text editor

General/XCode is not made for C? Huh? It seems to be made for Objective-C (which is a superset of C) and Java (which has very similar syntax to C) so in what way is General/XCode more suitable for C than a text editor? It has all the syntax highlighting plus a handy way of grouping files within a project, and compiling C code, what advantage does a plain old text editor have over that?

*I meant that General/XCode was not meant for developing General/CGIs, in any language. It lacks every feature you'd want in a CGI-focused IDE, like browser previews and remote file management. You could write scripts to do all that, but you could in a regular text editor as well.*

**Even vi has syntax highlighting.**
