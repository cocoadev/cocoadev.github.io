---
layout: page
title: CocoaDevRegistrations
---



I think it would be really cool if CocoaDev would keep a simple username/password account for me. It can keep track of an e-mail address. All information private, of course ;-) Logins could be cached w/ a cookie, so I don't have to reauthenticate from a given browser very often. Then:

* We can display more page stats: 
    page xxx
    created by yyy at zzz time
    last edit by aaa at bbb time

* I can sign up to be notified (via that e-mail) when specific pages change

* CocoaDevUsers would be more automatic. Every user gets a page. (everyone can edit users' pages though, keeping with the Wiki spirit)

* Moderators could 'lock'/'unlock' pages if necessary. Shouldn't be common though.



Obviously these are ambitious features. But I think we'd use them if we had them.

-- MikeTrent

----

I'm not entirely thrilled about the idea, but you're probably right: we'd use them if we had them.

What does the lord high executioner think? Steven?

-- RobRix

----

(Executioner?  Yikes!)

Ok, here's my thoughts.  I hope Mike doesn't think I'm picking on him.  :)  I'm just glad people are giving feedback!

Logins: The thing that makes me nervous about logins is it adds another potential step between having a thought and getting it into a page.  Yes, you can do tricks with cookies, and make the sessions last a long time, but sooner or later you will have to log in again.  

As dumb as it sounds, there have been times when I've wanted to contribute something to a web site, and been confronted with a login/registration screen and just  given up because it seemed like too much work.

So, if we did have a login system, we'd have to think of a way to make it optional.

I think Ward's Original Wiki had a system where you could associate a user name with your IP address via a page that just set a cookie on you.  There was still no accounting per se, but there were user names associated with edits.  

But what do we really gain from all this extra stuff?  Additional page stats is one thing that was mentioned, but does it really matter who created the page, or who was the last person to edit the page?  Before you answer, consider this page: http://c2.com/cgi/wiki?OurWordsNotYours

Email notification could be useful.  It might be more than the simple Wiki script can handle without some redesign.

Automated CocoaDevUsers is kind of another "who cares?" in my opinion.  If you want to be on there, you'll add yourself.  If everyone gets added automatically, it'll just be a bunch of links to blank pages.
 
Page locking feels dangerous to me.  I can't think of a reason I'd ever want a page to be locked.  I'm especially not fond of the idea of "moderators".  Everyone who comes here is automatically a moderator, and I don't think there's any reason certain people need to have special privileges.

A Wiki with logins and moderators and page locking sounds a lot like yet another Ultimate Bulletin Board site, doesn't it?


For further reading, I recommend reading: http://c2.com/cgi/wiki?WhyWikiWorks for some insight from people who have been doing this Wiki thing a lot longer than you or I.

I just want to reiterate -- THANK YOU! for offering suggestions.  I wish more people would!  I am very picky about certain Wiki philosophies, and feel I should explain my course of thinking when I shoot down ideas.  :)

-- StevenFrank

----

I'll have to agree with StevenFrank. I came to this site via a link from Mac NN (<-who wants a definition for that ;) I was intrigued by Wiki, I read up on it, and I really liked the idea. The main reason for my involvement in this site is **Wiki** and the way anyone has the ability to change and add to it.

- WillReiher

----

WRT: Timeout & Cookies, what's wrong with a time out date of the distant future? Not uncommon.

WRT: Hassle, let the logins be optional. People who just want to click-and-go can continue to do that. But people who want mail notifications or better edit tracking can enable that voluntarily.


WRT: user names assoc. w/ edits, that's something else I'd like, but it's impractical to do that w/ IP addresses. a cookie-w-long-timeout is much better, IMHO.

WRT: Page locking: I mention it only for completeness. Naturally we would not use it here. ;-)

I don't see how the first three WRT: points above violate the "wiki spirit" we've come to enjoy.

-- MikeTrent

----

I'm not completely against the idea if it could be implemented transparently -- ie; people who don't want to bother with logins can continue using the site exactly as they are now.  But people who do register can get notifications, etc.

I'm getting some ideas about ways this could be done unobtrusively.

I'll let it rattle around in my subconscious for a few days and see what comes out.

-- StevenFrank

----

I mentioned this on the Cocoa-dev list, but you might want to check out TWiki ( http://twiki.sourceforge.net ).  It's a Wiki, but with optional user accounts, optional page edit attribution, access control, and revision control.  You can either use it without the extras, just like a standard Wiki, or use as much as you need.  Personally, I like the revision control, because it means you can regress back any changes and negate malicious Wiki behavior.  But, I know that that's supposed to be a socially controlled thing in a Wiki.. still, it's nice to have the safeguard.

And another nice thing is the ability to 'subscribe' to Wiki pages, and receive email whenever they're changed, among other things...

Either way, great Wiki :)  (CocoaDev, that is.)

-- LeslieOrchard

----

There you have it: Wiki -and- Features. 

-- MikeTrent

----

The "Lord High Executioner" is a character from The Mikado. Don't you ever watch Gilbert & Sullivan, Steven? :)

-- RobRix

----

TWiki was one of the packages I considered back when I was first setting up CocoaDev.  It seemed overly complex.  But, I have more time now.  I'll re-download it and take a second look.

-- StevenFrank

----

TWiki's really not so bad or complicated.  Let me know if you play with it and if you need any help (only because I played with it for a few months).  The other thing I like is that it uses a simpler sort of text format with _underline_, /italics/, and *bold* as well as natural bullet lists that

* look
* like
* this

and tables

| that | look | just |
| kinda | like | this |

And there's a modular directive system where you can write new tags for text.  A few included are things like pre-cooked searches...  (Say I want to list all pages which have the text "Superclass: NSView" in my page documenting a subclass of NSView)

Only downside is that you would probably have to concoct a bit of a migration script to transform and insert all the current wiki pages into TWiki.

Anyway, enough advertisement.  You may end up not wanting to mess with what works already.  

-- LeslieOrchard

----

The longer you leave it to move, the more difficult the move becomes (more database content to switch over, etc).

-- FinlayDobbie

----

I'm starting a new topic: ShouldWeSwitchToTwiki

Let's meet up over there...

-- StevenFrank

