---
layout: page
title: ShouldWeSwitchToTwiki
---



So, as discussed in CocoaDevRegistrations, TWiki is a collaboration tool similar in many ways to this one.  Except where this is based on the "Original Wiki" code, TWiki has a number of enhancements, such as page version histories & diffs, email notifications of changes, and user accounting / registration.

So far, sounds good.  But after reinstalling TWiki, I still don't feel like I like it very much.  It seems like everything that was good and simple about Wiki has been complicated by a factor of 10.

But why don't you try it and tell me what you think.

http://www.cocoadev.com/twiki/bin/view.pl/Main/WebHome [note: this link is now dead]

My personal opinion: too complicated... Way too many options, too much text everywhere, and just basically ugly.

I've enabled accounting, so you will need to create an account to edit any pages on there.  Whether or not you can figure out how to do this will be our first usability test.  :)

-- StevenFrank

----

What about UseModWiki instead? It does CVS diffs, email notifications, and allows simple HTML and arbitrary wiki links using double square brackets instead of WordsJammedTogether. -pmb



http://www.usemod.com/

----

I agree [TWiki is too complex]- David Kopec

----

I second that.  The current setup works well for me, but I'm nice ;-)  Simon

----

Well I agree TWiki looks too complex at its current setup, isn't there an option to simplify it or something? If not, let's write a Wiki in Cocoa! :-) -- FinlayDobbie

----

Don't you mean in WebObjects, Finlay? :) My basic feeling is that all CocoaDev *really* needs is journalling...just like Mac OS X needs it :) -- RobRix

----

The TWiki you see at the above link is pretty much out of the box.  Yes, it could probably benefit from some customization.  But it's not clear where to start -- what can be done from within the program itself, and what requires source level editing.  Say for example, changing the font, which would be pretty high on my list of priorities, were we to switch.  Does anyone on here have TWiki experience that could help me make sure I'm not being unjustly harsh?

And the other question is whether making the customizations to TWiki would exceed the amount of time it would take to add the small set of extra features that seems to be desired to THIS Wiki.

Keep in mind that this current wiki consists of ONE tiny Perl script.  For everything it does, it is an amazingly compact piece of code.  Gotta hand it to Ward for thinking it up.

-- StevenFrank

----

I agree that this demo page looks horrid. But I'd still like features. If I had the time (see MikeTrent) I'd volunteer to look at your wiki script and take a stab at some of these things. But there you go ... 

-- MikeTrent

----

I had a few ideas last night about extending the current wiki that I think would be useful.  I think I will invest some time in them over the next few days, and put it up alongside the current site, so I can get everyone's feedback.

My ideas, in a nutshell, are:

- User registration.  This will allow user-level preferences and things like email notification of changes.  It will also provide a certain amount of accountability in case someone does something destructive.

Non-registered users will be able to browse and submit "annotations" (kind of like php.net's documentation system), so you won't necessarily need to go through the rigmarole of logging in to submit something, but only registered users will be able to actually edit the page content.

- Diffs.  Behind the scenes, use CVS to maintain a history of each page.  This complexity will be hidden from the user (unless they want to look at it for some reason)

- Integrated mailing list searching.  So, if you're at the NSDocument page, you can launch a search for mailing list items with "NSDocument" in the subject, let's say.  I have an almost complete archive of cocoa-dev, and most of MacOsxDev

- Connection to Apple's docs.  I haven't completely figured out how this would work, but basically, there would be a link between Apple's class documentation and this site.  It would be possible here to locally annotate Apple's documentation, however their material would not be reproduced here.  It would be more of an indirect "sourcing" of their site, so it wouldn't get out of date. 

- Simplified markup.  I think the lengthy tags I came up with for italics, bold, and so forth are too unwieldly

- Reciprocal linkage.  Pages will know which other pages link to them, and will offer a link back, forming a semi-intelligent automatic "see also" section

And I intend to do these things with as little change to the existing user interface of this site as possible.

Thoughts?

-- StevenFrank

----

Seems to me the biggest problem with cocoadev is that people don't dare modify somebody else's prose and just add stuff at the bottom. This results in having some very hard to read pages. 

An idea I have just had: why don't you make the diffs available to everyone (not user accounts) so that  if somebody sees something has changed for the worse then they can change it themselves. This would reduce the reluctance to edit something somebody has writen as that somebody can put back what he/she wrote. This would also make policing the responsibility of everyone instead of just you.

I'm not sure I'm beeing very clear. 
What I really think is that you should keep the initial idea of cocoadev which is first to get the info out there. Anything that makes that harder is not  good. I probably wouldn't of written this if I had had to login (but then maybe that would of been a good thing)

You could make accounts optional for email notification and such. 

-- FrancoisFrisch

----

Not to worry, I understand the importance of making quick, anonymous edits, and the registration thing won't be a barrier to that.

Tonight, I completed the first phase of the upgrade, which was to convert the Wiki code from Perl to PHP.  This is something I've wanted to do for a long time, as it will lighten the load on our server, make it respond faster, and the code is a bit easier to read.

Here is a preview / beta-test version for you all to experiment with:

http://www.cocoadev.com/mimi [currently not active]

Disclaimer: I haven't tried it on anything other than Mac Internet Explorer yet.

Notice that I moved all the database contents over, so you can see what it will look like, however that database is not linked to this one, and **any changes made on the beta site will eventually be lost.**

I only had time to add two new features: First, the "recently viewed" history trail.  Second, the automatic "see also" section at the bottom of each page, which works better than I thought it would.

Let me know what you think.  I hope to attack some more of the things I mentioned above in the coming days.

-- StevenFrank

----

I like it. There is a bug (I think it's abug) in the recently viewed feature. When you view a page twice it adds it twice instead of puting it to the top.
FrancoisFrisch

BTW: http://wodev.spearway.com will look familiar.

----

I added a page for suggestions from the beta test on the beta test site. Maybe it should have gone here. I don't know. I'm tired. Anyhow, it's there now.

-- RobRix

----

Steven have you ever thought of doing it in WebObjects? 

----

Have a look at http://www.bluetail.com/wiki/
for an example of diffs and accounts.

and http://lightingwiki.com/WikiSandBox?action=edit for complicated formating (too complicated) But I like the multiple titles.

FrancoisFrisch

----

I didn't really consider WebObjects, because I'm much more familiar with PHP, and it's free.  :)

Last night I added the preliminary support for "including" Apple's current docs on all of the "NS"* pages.  Check it out by going to the beta site, and choosing any NS class link.  You should get the wiki page, followed by Apple's own docs as retrieved from their web site.  This allows us to effectively annotate the docs at Apple's site.

It's not quite perfect, but it basically works now.  There's still a few bugs I need to track down.  Let me know if you find anything really weird.

-- StevenFrank
----
 Cool!

However it doesn't work for java classes (NSAlertSheet) and a lot of classes are obj-C AND java. They are only obj-C in the beta site. 

Also in NSActionCell if you click on  a     - setAction:   in one of the method's "see also" links you get to wiki page where as all the others(as far as I can see) work fine. Strange!

The problem with the way it is displayed is that it is hard to see the cocadev additions (you have to scroll down).

Do you think it would be possible to parse the page from apple for <h3> tags (they only seem to occur for method names) and you could keep a dictionary of cocoadev additons for each method name. 

-- FrancoisFrisch
----

I'm not sure how to handle Java documentation.  Obj-C is my preferred Cocoa language, so I'm not motivated to find a solution.  But if anyone has any ideas on how that should be handled, I'm open to suggestions.

Would it be better if the wiki additions appeared ABOVE Apple's docs?

Having pages for the individual methods is a good idea, but they don't match wiki naming conventions...  Perhaps anything beginning with "-" or "+" should become a wiki link?  That once again leaves Java users out cold.

Hmmm..  I'm going to need to think more on this one...

-- StevenFrank

----

Hmm, seeing as I think I was one of the people who first let loose the bee into bonnets about TWiki...

StevenFrank: *My personal opinion: too complicated... Way too many options, too much text everywhere, and just basically ugly*

I really wish someone would revamp that default install for those guys.  I would, but I don't have time.  They manage to cram every single feature into the first page, and it just looks nasty.  First thing I did when I installed a TWiki was to strip it down to look not unlike the front page of this site.

So, I kinda know where to start in doing the customization, although it's been about 5 months since I last played with it.  I could probably pick it up quick, if you want to switch and want help.

TWiki has a lot of the features you want:

StevenFrank: *My ideas, in a nutshell, are: 

- User registration.* Has this, though in a funky but working form.  (I say funky because all the sites I work on now have an elegant LDAP integration going on.)  Maybe they've improved it.

*- Diffs.*  Uses CVS on the backend to track all changes.

*- Integrated mailing list searching.*  Doesn't do this, but if I recall, it is pretty modular, so we could drop something like that in... depends on the how, where, what of the lists.

*- Connection to Apple's docs.*  If anything, maybe write some perl to import Apple docs into Wiki pages, and have some way to update those pages when new docs come out without destroying commentary?

*- Simplified markup. *  It definitely has this.  It's a central feature of TWiki.  If anything, you might want to consider ripping this bit of regex out of the TWiki source and pasting it into your own customized Wiki

*- Reciprocal linkage. *  Doesn't have this, but I seem to remember someone working on a "plugin" for this.

So, it looks like TWiki has 3 out of 6 of your wishlist.  How hard would all six be to implement on your existing Wiki, and how hard would it be to grok TWiki to implement the remaining 3?  

The interface can be fairly easily stripped down to barebones so we can get rid of the nastiness everyone's looking at right now.

Maybe I'll go download TWiki again and dig in to see if there's anything else I could say about it.

-- LesOrchard

----

We've just rewritten WODev in WebObjects (It used to be exactly the same perl script as cocoadev) and we are thinking of implementing the idea I had about the Apple Docs. One of the many advantages of WebObjects is that it is very easy to add features and such. It also has a database back end which is an advantage and a disadvantage (making diffs available is a lot harder)

Well anyway I thought I'd just let you know.

-- FrancoisFrisch

----

I like the layout the beta site. The recent list seems very nice. Still holding out for even more clever features ;-)

As for WebObjects, I too favor the simplicity and elegance of Perl. Ultimately if WO will fit the bill faster than Perl I'm all for it. But I suspect Perl is the right choice for our illustrious site maintainer.

-- MikeTrent

----

Just out of curiosity: Is there any chance the beta will actually become the primary site one of these days? (And if there is, will my pending purchase of the lastest version of Audion help speed things up? ;-) )

-- JensBaumeister

----

Diff and permanent history records would be very useful when morons come and deface a page. You could roll back to the last decent version.

-- KritTer

Yeah... this isn't the first time we've run into this problem. The CocoaDevToDoList went through about five incarnations or so...

-- RobRix

Is there any way of getting the IP of people who deface pages?

-- KritTer

----

As long as we're experimenting (are you still experimenting?), how about adding a feature that makes an rss.xml file with the contents of the Recent Changes page? That way users of programs like Radio UserLand (http://radio.userland.com/ ) could *Subscribe* to CocoaDev, and that would be very cool.

Of course, I'm just assuming that making that file would be easy - but it really does seem that way.

-- MichaelMcCracken

----

Hey, the RSS subscription thing is a great idea.  Maybe we could hack something up with Stapler or RssDistiller (is the second one free)?  I've been meaning to do so.

And, indeed, I've done so with RssDistiller: the RSS feed for CocoaDev is at http://web.sabi.net/rss/cocoaDevRecentChanges.xml.  Enjoy!

-- NicholasRiley

----

Wow, thanks!

Nice job, Nicholas - That's some quick turnaround on a suggestion!

I'm already using it - excellent. Radio users can click here: 

http://127.0.0.1:5335/system/pages/subscriptions?url=http://web.sabi.net/rss/cocoaDevRecentChanges.xml

to subscribe to cocoadev's recent changes. It's the equivalent of the XML coffee mug on a Radio weblog.

--MichaelMcCracken

----

The feed is back after a multi-month outage. I've been getting it myself; the WebDAV upstreaming to publish it was just broken; sorry.

-- NicholasRiley

I for one love the RSS feed - Is there any chance the title attribute could be swapped from the time changed to the page changed?

--DiggoryLaycock

----

If we're switching engines, I'd like to recommend that we drop the current syntax for marking up text.  It makes pages very hard to read and I'm sure it's intimidating to beginners.  We should be using the "standard" wiki syntax, like what http://wikipedia.org and http://phpwiki.sourceforge.net/ use.  In the traditional syntax even complex pages still have simple mark up.

-- SaileshAgrawal

We're not switching engines. Standard Wiki syntax was **not** designed to make adding code easy. On a personal note, I didn't find CocoaDev intimidating, and I'd never used a Wiki before. Coming from a coding/markup background, I'd probably have found the ideosyncratic markup of standard Wikis more perplexing than %%s. But some better middle ground, that I'd plump for. -- KritTer

----

Standards wiki syntax resemble traditional plain text. For exaple, MoinMoin syntax: = Title =, == Title 2 ==, **Bold**, *Italic*, ---- (horizontal line), `inline code`,      Block of code  etc. I think this syntax is much easier to read and write. 


* MoinMoin is here: http://moin.sourceforge.net
* MoinMoin developers wiki is here: http://twistedmatrix.com/users/jh.twistd/moin/moin.cgi/
* MacMac, MoinMoin Hebrew wiki site is here: http://mac.plonter.co.il/plonwiki


--NirSoffer

----

It's the syntax for italic I was worried about, but I've just realised * isn't valid in ObjC anyway. Sweet. On this note, I'm going to get in touch with StevenFrank and see whether he's amenable to a switch.

In the meantime, let's have a community vote. -- KritTer

**Should we add standard Wiki markup to CocoaDev's parser?**

Ayes: 3
Nays: 0
Fences: 0

Don't know who just voted aye, but I think they've come a year late....

*Better late than never...*

----
It's been a while since I last took a look at various Wiki technologies. To start with, this wiki is fine, it works quite OK, and there's a lot of good programming gone into this implementation. Anyway, if I would do a similar site, my favourite technology out there is MediaWiki (the engine powering WIkiPedia and similar web sites). The reason is that it has a MySQL backend and uses PHP, two things that will minimize performance issues, not that the MediaWiki is much bigger than usemod and similar lightweight wiki systems. The other thing I like about MediaWiki is that it has a clean editing environment for histories, comments, and forces people to login and this way keep the content editable without guessing who did what, and when. The discussion tag is especially helpful as a lot of side discussions could be moved out from the main page. Also, MediaWiki avoids using CamelCase so it looks more like a normal web site, and makes it possible to build links of any name structure (that then shows up in google searches, too).

Anyway, I'm sure this site could evolve into something similar by adding similar features along the way. Also, it's quite a pain to move content from one structure to a new wiki system, it's like starting all over in many cases (MediaWiki actually allows to migrate usemod databases over). 

http://www.onlamp.com/pub/a/onlamp/2004/11/04/which_wiki.html has a review of the common Wikis. As an update to this review, the initial configuration of MediaWiki could now be done via a browser session.

--Kent

