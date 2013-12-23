---
layout: page
title: PerlLang
---




Perl is one of the most popular programming languages and it comes 
pre-installed with any copy of Mac OS X. It lives in /usr/bin/perl on your computer. 

Perl is a general purpose language and is used to perform a wide range of
tasks (although there is a common misconception that it is just a ScriptingLanguage for quick and dirty operations).
Its traditional strengths lie in text processing (Perl has an integrated powerful pattern-matching engine), UNIX shell scripting and CGI (dynamic web pages), but you can develop GUI applications as well.

There is a strong developers' community behind Perl and they are usually very eager to help out with questions about it. A good starting point is the Perl directory at http://www.perl.org/

A good place to lurk (and eventually ask questions) while you're learning Perl is http://www.perlmonks.org/

Unlike some forums, the members run the gamut of expertise from absolute beginners to people actually working on the next implementation of the language.

----

This WikiWikiWeb is implemented in perl. A single perl script does it all, in fact.

----

Perl is famous for the fact that "there is more than one way to do it" (TIMTOWTDI), the same Perl script can be written in a hundred different ways, more or less readable, but usually less.

This is how one would read all the lines of a file and output them on STDOUT (could be made shorter, maybe a JAPH [Just Another Perl Hacker] could try?):

    

perl -e 'print <>' filename



Okay, here's a shorter one:
    

perl -pe * filename




----

Perhaps the best feature of Perl is how integrated pattern matching is to the whole language; with Perl 6, it will supposedly get a whole lot more so, but that's a little while off yet.

----

Another good thing about it is that Perl is very cross-platform (most Perl programs work on Mac, UNIX and Windows).

----

CamelBones is an ObjC bridge for Perl. It allows you to write Cocoa apps in Perl or use Perl from Objective-C.

You could also have a look at PerlObjCBridge.

----

Mac::Glue [http://search.cpan.org/~cnandor/Mac-Glue/] allows Perl to communicate with AppleScriptable applications.

----

PerlPad is a Cocoa app (based on CamelBones) that lets you execute Perl statements as a System Service.

