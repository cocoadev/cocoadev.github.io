---
layout: page
title: NewPageDiscussion
---

I'd like to begin a discussion about when one should create a new page and when one should not. I would like to have some kind of consensus on what is considered useful or worthwhile to the site and what is useless and shouldn't be bothered with.

On one side of the fence, I trashed a page created that referred to the NSModule API as being useless and talking about an obsolete API. After some discussion with the creator of the page, I changed my mind and realized that it could be useful to have, just to warn people away from it, or to satisfy the curious, or some other thing I couldn't think of.

And then this page was prompted by my creation of the JustWork page, which was immediately trashed and placed in DeleteMe as being useless. I think that JustWork is an extremely important concept for creating useful Mac OS X programs and deserves a page.

In my opinion, I think any concept which is vaguely related to Cocoa programming ought to have a page. If it's created with the most common name for that concept, it's likely to be found and linked to over time even if there are no links to it immediately. I don't think the server is having issues with storage space, and it's not going to clutter anything up, so I can't see a reason not to post about anything that's OnTopic.

Note that this page isn't sour grapes; I just think it would be useful to the site as a whole if we could come to an agreement about this issue. If the consensus is that JustWork and any similar page should be tossed in the junk heap and deleted, I have no problem with that. -- MikeAsh
----
I agree that we need guidelines for this kind of thing, but since DeleteMe is almost never emptied we should also try to fix pages created by mistake. If you go to NSTextFieldDelegate you can see a good example of this. Look in the history, and you'll find that someone (me) posted an anonymous comment saying how the page was useless. Someone else (also anonymous) posted below saying how that was even less helpful than the page itself. So in an effort to make amends, I went and looked at the methods that yet another person had found, and actually discussed them this time. I still don't think the page is helpful, but the other person was right -- better to make the page as good as possible for now. In this case, DeleteOnceRestoreOnce is also a good guideline. If someone thinks a page is valuable, they will remove it from DeleteMe.

Even so, some pages really are better off deleted, such as *totally* off-topic things, mispelled page names, and pages with requests for help with bugs that turn out to be typos (no offense, sometimes it takes more than one person to catch these). --JediKnil

*I don't think it's possible to create such guidelines. Whether a page is deleted or not is a process of consensus. Take JustWork: I couldn't see any value, neither could another contributor, but you can. Great! So the page develops, the DeleteMe flag is removed, and it develops value, rather than just being a ShallowPage created because somebody smashed two words together. Marking a page with DeleteMe is just an opinion, like any other, and subject to dissent and removal, like any other.*

----

The thing is, adding DeleteMe accomplishes exactly two things.

* It chides the original page creator.
* It informs a browser that this is not an important page.


The second can be useful, but only when it's really incontrovertible.  e.g., for a page whose content has been factored into a different page.  The first is probably not very effective, though it's probably sometimes necessary.

What bothers me is when too many pages turn into discussions _of_ the pages, or even turn into discussions at all.  I love the expository pages, like DebuggingTechniques.  One solution would be to associate a 'discussion' page with every normal page, as wikipedia does.  This would require a modification to the perl script powering the site, but StevenFrank seems to pretty amenable to changes.

----

*What I've done a few times on pages with weighty discussions tacked onto the end of the *real* topic is just create a new page "<original page name>Discussion", put the threadMode discussion there, and leave a link to it on the original page. Simple, effective, and it seems to keep everybody happy.*

*Another page that seems to be in somewhat the same situation as JustWork is NSTextFieldDelegate. The page was created by accident, I made a start on attempting to put some worthwile content on it, and the page was immediately DeleteMe'd with a note "there's not enough information on this page to keep it around." Well, the page is going to stick around basically forever whether it's listed on DeleteMe or not, so why not try to make the page at least worth a click. Someone could come along with exactly the right content to fit the page in the future.*

threadMode - http://c2.com/cgi/wiki?ThreadMode

**If you've good info to put on, sure. If not, you can orphan the page (break the links to it) and nobody's going to waste their click in the first place :)**

----

I would just like to note that short != valueless, and long != valuable. Remember that elegance is achieved, not when there is nothing left to add, but rather when there is nothing left to remove. �DustinVoss

----
I think that StevenFrank should set up an automatic garbage-collecting spider. It could be rule based, and the community could vote on rules or ask to have rules changed (Just a thought, not really important.) What I think it should do is:
Check how old the page is: If It's more than 6 months old, lets say, tag it.
Check the tagged pages for backlinks: if there are more than, say, 10 pages that link to it, it stays for another 6 months. (If those pages with links to it get deleted, well)
This could be abused by inserting a link into a discussion (Like, in a java vs. obj-c discussion, saying "Well, JavaSucks") or something, and it's prone to abuse, but it could work if we had the right checks & balances.

----

An automatic DeleteMe spider might be useful; it could check if the page is in DeleteMe and hasn't been edited for some time (6 months sounds good) and then trash the page.

Anything else, though, sounds like a bad idea. It sounds like you want to delete every idle page without enough links to it. A page doesn't have to have a lot of backlinks or activity to be useful. I can easily imagine a page which is useful but has few backlinks, simply because it's not a topic which comes up when writing about other topics.

There is a lot of focus about deleting pages, and how to best go about it. The server doesn't have any large restrictions on storage space that would impact the wiki. Pages without backlinks don't clutter anything because they don't appear anywhere. They "clutter" search results, but if they showed up in the search results for something then it's quite likely that it's because the page was actually relevant to the given search. So why the big rush to delete pages, even worthless ones? -- MikeAsh

----

*I can easily imagine a page which is useful but has few backlinks, simply because it's not a topic which comes up when writing about other topics.*

I contend it's not all that useful if you can't find it *easily* in a directed search. It is fairly easy to overestimate the usefulness of a page,
(or underestimate its uselessness) if you consider the following:

*Pages without backlinks don't clutter anything because they don't appear anywhere.*

But they **do** appear somewhere. Exactly those cluttered search results. The assertion that they are likely to be relevant because they contain the
search term is without foundation. In 45 seconds I found (for example) CorruptedHeaderInNSTableView. There are many more like it. Many, *many* more.
The "likelihood" is lower than one might guess.

Or take CannotSelectInTableView. This is not really about NSTableView, or even about selection. If you read it, all you learn is that a neophyte
found out something very basic and general about MemoryManagement. This is a one-off deal, because MemoryManagement is adequately covered
elsewhere. The poster's problem was solved, but how is this page likely (and I emphasize, in the way MikeAsh means *likely*)
to help someone else in a way that MemoryManagement does not?

**This could alternatively be solved by allowing new pages to be made without a title, relying solely on backlinks (    BEGINENTRY) for directed searches. That way, clearing the text from a page is tantamount to erasing it: without a title, it won't come up in searches any more. Now, however, we have a better, consensus-based deletion system: see DeleteMe.**

----

Just a few thoughts. (1) Any kind of garbage-collecting spider is a really bad idea, though I'm not going to take the time to argue that rigourously. (2) We don't actually have the problem this page is worried about. You only need to reach consensus about things *in general* if they crop up all the time, and they don't. So we can reach consensus on the pages as they crop up. (3) We're always going to have newcomers creating pages with silly names, even if we decide they shouldn't. So why bother?

I suggest we orphan this page.

----

From JustTestingNewPageCreation

    DeleteMe - are you illiterate or did you just not read HowToUseThisSite

This is uncalled for.  Accept that this is going to happen.

----

Welcome to NewPageDiscussion, round 2!  The topic:  What's off-topic, and what do we do with off-topic pages?

First, the vote from TeeVeeOperatingSystem. 

Pages like TeeVeeOperatingSystem..

* should be marked DeleteMe and blanked (5, incl. original creator's vote)
* are fine and should be left alone (3)
* are not to be encouraged, but arguing about them is worse than either deleting or keeping them, and at present levels off-topic pages are not impacting the site (so if there's argument, keep) (5)
* should be marked DeleteMe, but the content should be left there for others to decide whether or not to keep it -- see DeleteOnceRestoreOnce (1) Why wasn't this here before?


(If these options don't cut it for you, add an option)

I think a principle from wikipedia is that votes should only follow an attempt to build consensus.  Um, oops, sorry about that. ;-)  Also, I expect the real vote, should it eventually occur, should be on TeeVeeOperatingSystem, not on pages 'like' TeeVeeOperatingSystem'.  Anyway, if people want to keep voting, that's fine, but it's clear that there is not currently consensus.  Discuss.

**
As the original creator of the page, I know I have gone too far with being off-topic. I won't revert it, and I vote for blanking and DeleteMe<nowiki/>ing it.
If we allow this sort of pages, the signal-to-noise ratio is going to lower, which isn't good, especially in a wiki.
**

*I say we forget the vote and just add it to the oft-forgotten "OffTopic" page. Rather than using the begin/end entry flag to flag it as a DeleteMe, flag it as an OffTopic. Since DeleteMe pages are rarely deleted, it's ineffective. Blanking out the contents only adds insult to injury, as it were, so we can avoid an all-out war (and a useless 'vote') by leaving the contents but flagging it as OffTopic. I've taken the liberty with TeeVeeOperatingSystem. Yes, it is funny. I admit I even sort of spurred it on (by responding to that CerealKiller page, but someone *else* created and filled in the stub). However, it *is* off-topic. As long as the original author is mature enough to accept such a label, there should be no problems. If it honestly *is* Cocoa-related, the community will decide. If it is an *offensive* off-topic page (there have been more than a few), *then* it can be marked as DeleteMe.*

**Actually, DeleteMe is being maintained just as the page specifies.**

*Ah! Has this changed recently? Then I withdraw my previous statement. Burn 'em! Burn 'em all!* **Heheh...**

Yes, this just changed about a month ago. (Around May 2005, for those reading from the Mysterious Future.) Now pages in DeleteMe that aren't touched for a month get trashed.

**Greetings from the Mysterious Future!  Wish you were here..**
----

