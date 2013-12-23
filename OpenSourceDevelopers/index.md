---
layout: page
title: OpenSourceDevelopers
---

First of all, I know hardly any Cocoa. But, I believe I have a great concept for a rather complex application. I would love to start an open source project for this app (I'm a big fan of open source), but I don't know how to do so, noting that there's no existing code.

Does anyone have any suggestions as to how I might start this? I'd love to get devs interested in this project, but I don't know where to get started.

Thanks! :-)

 - Oliver

----

You could create the project at SourceForge, but most developers scratch their own itch, so if you do not provide an initial implementation which looks promising, I'd not be too optimistic about finding developers, unless your idea itself has an interesting potential.

Why not tell about it here? That might also lead to some better advice.

--AllanOdgaard

----

Ah, I've got a rather complex idea scratched out across paper, my mind, and my computer... I'll post it online when I have it in a human-readable form (as it is now, I'm lucky that *I* can read it. This concept app is basically a word processor. I'm a general computer consultant (for normal people, not companies), and the majority of the complaints I receive about software have to do with word processors sucking (miserably, in most cases).

Out of all the applications I use, ranging from OmniWeb and AdiumX to Photoshop and Final Cut Pro, I'm most disappointed with word processors. I keep SubEthaEdit in my dock - it's my favorite editor due to lack of bloat - and use it for most anything, including outlining, jotting things down, web site creation, and even writing full-fledged reports.

Most every complaint my customers and I have regarding word processors would be solved with a CLEAN and USABLE implementation of the features I have strewn about, which include:

* Automatic Saving
* (reasonably) Unlimited Undos, even across sessions
* Saving "without folders" (actually use Application Support/)... allow an unlimited number of document categories... a document can be in multiple categories at once
* Some fast, easily-accessible by-content searching engine (I wrote this before Spotlight was announced, which pretty much fits this description)
* Use of style sheets (not CSS) to allow for a document to be output in different ways without reformatting everything, and to allow for changing the style of a particular 'class' of text (without first highlighting every instance of that 'class', and without using clumsy "Smart" features)
* Simple exporting to TXT, RTF, DOC, PDF, HTML, (e)mail
* [Entire Document Library]-wide find and replace
* Referencing a document 'part' inside a document, kind of like HTML's <IFRAME>s - e.g., referencing a part the user could call "Casual Footer" would insert the data, which could include the user's name, telno, email address, and mailing address. When the document 'part' changes, all documents that reference that 'part' will also change.
* ** XML or XML-based format
* If someone could figure out a good way to implement it, TOCs, indexes, footnotes, endnotes, and things to that effect would be nice.
* RTF 'scratchpads' that are document-wide or category-wide, and provide the user with a nice way to organize thoughts.



Bold items were added after this list was first posted.**

Excuse the messiness and incompleteness of the above list; those features are just some of many that I've outlined.

Any feedback would be welcomed :).

 - Oliver

----

Great plan! I want that too. I hope some real developers (I am not really!) can help make your dream come true. Just my 2 cents: I was on the subversion web site this morning (http://subversion.tigris.org) and I just thought while reading your idea that Subversion could already give some of the functionality you are describing (unlimited Undos/history, automatic saving made easier, saving without folders in the repository). Have you also checked for existing projects similar to your idea? --CharlesParnot

----

Yeah, I've looked around for apps labeling themselves 'a better word processor,' and really, nothing comes close to this ideal feature set. Someone please correct me if I'm missing a lovely app :). There's LetterStar [http://objectpark.net/ls-download.html] - basically TextEdit with a few extra features, and Mellel [http://www.redlers.com/mellel.html], but neither of them solve many problems.

Here's something I had been thinking of, but I hadn't figured how to put into words:
"White space should not have style, font or pagination properties that are different from the surrounding text.  In word, when I delete some white space sometimes entire paragraphs and pages change font, size and style all at once.  That's just wrong.  Just very, very wrong." - from AppleAppsMostWanted.

Any developers with an inordinate amount of free time? Anyone? No, didn't think so :).

 - Oliver

----

Are you aware of LaTeX? Here you can change the output using style-sheets, you can export to PS, PDF, HTML etc. and it also allows you to include other files, e.g. standard header/footers.

The barrier of entry however might not be low, and this is probably the real problem, i.e. the superuser will use a revision control system (for the unlimited undo across editing sessions), a markup language a la LaTeX (for the reasons above and more), a rather powerful editor like Vi or Emacs (which he can fully customize to act like he wants it to, including writing macros/scripts to add/replace functionality etc.) supplemented by sed, find, grep, and similar from the Unix toolbox (for searching in several files, doing file level search'n'replace, conversions and more), and this will give him almost infinite power in his editing tasks -- but all this requires a lot from the user if he wants to master these "tools", and most likely only programmers will end up using such a tool chain.

Now if I understand you correctly, you'd like to have similar functionality but without the hassle.  Rather than write a new program to incorporate all the ideas, I think another approach could be to try and make a nice wrapper for some of the existing tools.

A friend of mine wrote a Wiki -- http://www.instiki.org/ (there is an OS X download which has it bundled as a single double-clickable application you start, and then it serves the Wiki on port 2500 or so, no setup required) -- the interesting thing here is, that it uses a very simple markup language, but can be converted to LaTeX and thus exported as PDF (directly from the web-page, though if you run it on your own machine, you'll need to install LaTeX separately).

There are revision history on the pages (like with this Wiki, but with changes highlighted) and there is of cause also search functions.

I am mentioning it because my friend cowrote his bachelor project (with 4 others IIRC) using the system, and as I understood it, it was a big success, especially for the other people involved, who would not normally use LaTeX, a revision control system, or split up one paper into different sections included by the top level sections (table of contents etc.).

The downside is that you'll need to do all the editing in the browser (the upside is that you do not need to install anything, you get revision control, and you have access to your documents from anywhere there is internet -- granted you have the Wiki running on some server).

However, the next step for this Wiki might be to introduce a custom URL scheme for page edits, so that editing pages on the server would be done using a local editor -- possible something like SubEthaEdit, so that if two people edit the same page simultaneously, it would let them share that same document and allow for collaborative editing.

I don't know if this sounds like something which could be another solution for you and your clients? As I see it, most of the features you want are really external, and writing a new word processor is a big task, I know, as I am currently writing a programmers editor! ;)

--AllanOdgaard

----

Because I use a laptop (they seem to be quite popular in this little podunk town) and  run about constantly, I usually don't have access to the internet, so a Wiki wouldn't be that great of an idea for fulfilling my document-editing dreams. LaTeX would be a good thing to integrate - for some reason, I never thought of that. I'll be reading more about it :).

 - Oliver

----

Oliver, this sounds like the sort of app that many people would be interested in. Why would you want to make it open source? Have you got that much disposable income and free time that you want to give away something like this for free?

Maybe I just don't understand the open source thing.

- anon

*open source software doesn't have to be free (beer.) especially for something like this, which is likely to attract a lot of non-technical, non-developer users who don't have the wherewithal to download & build something from source.*

----

Good question - I was really just throwing the open source idea around in my head. No, I don't have such resources to just pour into a project like this :). I'd just really like to see this idea realized...

Feedback is always welcome :).

 - Oliver

----

I've been a capitalist ever since my first child came along. Would you consider this as a commercial product? find me here: REMOVED

- er, anon

----

You've got mail!

 - Oddly Familiar Male Voice

----

I found a  word processor project called Tekstilo on SourceForge.  They are still in a very early stage of development, I'm sure they would be glad to see your ideas.  Maybe they will let you join the project, who knows.  You can find it at http://sourceforge.net/projects/tekstilo

----

What about DocBook + CSS, that would be a great combo. Or perhaps write a DocBook editor with a functioning XSL-FO renderer (FOP is worthless).

--TheoHultberg/Iconara

----

I have become a BIG fan of LaTeX. I use it all the time for mathematical formulas in research papers, proposals, etc. Currently, I use the tools:

LaTeX Equation Editor - http://evolve.lse.ac.uk/software/EquationEditor/
TeX FoG - http://homepage.mac.com/marco_coisson/MacOSXsoftware.html

And import it as a .pdf into Adobe InDesign. Really, InDesign is overkill for most things. If you could base the text editor on LaTeX and make it easy to use (not having to know or remember all the commands) I could see that as a very successful product. The fact that it can do equations and tables means it can really fit the bill for technical and scientific writing. 

PhilipRiggs

----
Oliver: you dismissed the idea of the Wiki based on the idea that you don't have access to internet all the time. But in fact, you only want the wiki on the web if you need to share the documents. But I don't think this is what you want to do, otherwise you would not be able to do so because you are not connected to the internet all the time (am I repeating myself here or just turning in circles???). Anyway: you can have the wiki server locally on your computer, and access it on your web browser without the need for an internet connection. In other words, you have a web browser-based app. That could be a good starting point. And you could always have a local wiki (for personal docs) AND a server-based wiki (for shared docs). --CharlesParnot

----

Indeed, and the Wiki requires no setup for OS X, comes as one app on a disk image which you doubleclick, and it stores the data in ~/Library/Application Support.

It uses MarkDown for the syntax, which I wish this Wiki would also use! :) There is a perl script to convert MarkDown to HTML, and this Wiki is already using Perl, so perhaps it could be possible? Although all existing pages would have to be converted.

--AllanOdgaard

----
Dear Cocoa dev'ers

There is an exciting set of ideas on this page.  As the OP and primary contributer of AppleAppsMostWanted page, I want to let you know that there was a call for developers on the page -- I even contacted some of you personally by email to implement the ideas enumerated on that page and partially duplicated on this one.  I got few responses, and none that came from developers seriously interested in spending/wasting their time on such a project.  In the end I went ahead with implementing a WP with an old coding buddy of mine.  We have a fairly mature code base at this point and have some exciting features not mentioned on this page or the earlier one.  We are about one month away from release at this point, but would still welcome any other developers, provided they are in a position to contribute.  Our model is most likely opensource shareware.  We are all about supporting standards and introduce no proprietary formats whatsoever (besides supporting ones like .doc).  We'd hate to see two identical apps released by the same community, instead of consolidating our efforts.  If interested, please feel free to contact us at:  nsobject@gmail.com

