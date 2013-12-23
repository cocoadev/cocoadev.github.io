---
layout: page
title: SubVersion
---



download from: http://subversion.tigris.org/

read manual at: http://svnbook.red-bean.com/

nice article at General/MacDevCenter: http://www.macdevcenter.com/pub/a/mac/2004/08/10/subversion.html

basic into screencast: http://clickablebliss.com/blog/2006/04/26/introduction_to_subversion_screencast/

*I've also done a write up of setting up svn on my own linux server here - http://vinay.afternight.org/Wiki/index.pl?General/SubversionCollection - I'll keep updating those pages.   It's mainly a write up for myself for the future.  But hopefully it will be understandable to others as well.  - Vinay*

*Another article from me that links to others and adds more info: - http://zathras.de/angelweb/x2004-10-12.htm (There's also more at http://zathras.de/angelweb/x2005-02-08.htm and mentions of a few bugs and gotchas at http://zathras.de/angelweb/x2005-05-18.htm and http://zathras.de/angelweb/x2005-01-05.htm - hope it helps)  - General/UliKusterer*

As of General/XCode 1.5, you can use General/XcodeWithSVN.

----

Also known as svn.

Subversion is basically CVS-done-right combined with General/WebDAV. You can move files, remove directories and all the other things that you can't with CVS. Subversion is just as easy to set up as CVS, and will work against a local repository, a General/WebDAV hosted one, or over SSH to a machine with a local directory. There is also a custom server process if you'd like.

(Subversion does NOT require Apache to be installed)

--General/TheoHultberg/Iconara

Useful commands:

    svnadmin create

    svn checkout

    svn update

    svn stat

    svn commit -m "message"

    svn diff

    svn differs from     cvs as the revisions are done not per file but per branch so each revision brings the whole branch forward rather than just a file. This adds no real disadvantages while being more reasonable and making the revision number more useful.

A nice side effect of this is that commits are atomic - you can roll in and roll out changes to a bunch of files  at once, rather than having to do it piece-meal with CVS.

----

A heads up to those of you thinking of going down the SVN route to manage your source repositories.  There is a fantastic web front-end called Trac (http://trac.edgewall.com ) that feels something like Bugzilla but without the cruft.  It's a repository browser (which lets you see a nicely formatted html diff from revision to revision), a bug tracker, a project planner and a wiki all in one.  It really is top software, well worth checking out....

More info on the website or in #trac on freenode.net.  

JKP


----

Some open source Mac projects have switched to Subversion with great success (and some use Trac also).


* Colloquy: http://colloquy.info (Trac: http://project.colloquy.info/trac )
* Growl: http://growl.info (Trac: http://trac.growl.info/trac )
* Adium: http://adiumx.com (Trac: http://trac.adiumx.com )


---- 

For those looking for a simple repository viewer/manager on OS X should check out svnX : http://www.lachoseinteractive.net/en/community/subversion/svnx/features/

-Seb
----


There's also General/SCPlugin, a context menu module for Finder: http://scplugin.tigris.org -- General/UliKusterer

----

I know it is going to look stuppid, but I only recently started to use svn, and found that you don't need the server side when you just want to use it on a local repository... I really thought there was the need for a server running, and only finally decided to use General/SubVersion when I learnt about the svnserver possibility (I did not want to fight with Apache2). But it turns out just the client is enough for local repository accessed locally, which is fine for my own little projects. Now, you have all these lines below to boo me for being that short-sighted...

*Oh, BOOOOO ... how could you **possibly** not know everything about every technology out there? Your geek license is hereby revoked!* ;-)

*It isn't shortsighted since you can always install the server later/move your repository wherever you like.*

----

**Subversion Hosting**


*
https://opensvn.csie.org free, but slow access times (I get average of 200ms ping from my computer).
*
http://www.surpasshosting.com/webdev - $10 a month but is an actual full blown web host (so additional things like General/WebSVN, Trac could potentially be used)
*
http://www.textdrive.com/ - variety of plans, but they'll support not only subversion but an entire trac development instantiation for roughly $12/mo
