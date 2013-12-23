---
layout: page
title: CocoaDevMostWanted
---

We don't want to seem like a bunch of thankless mooches, but this page is for ideas that might make CocoaDev even radder. For those who are searching, the phrase 'feature request' should be on here.  And now it is. :)

See also CocoaDevToDoList.

----

**Most Popular** - items with at least 3 more AYES than NAYS



* A search field at the top-right of every CocoaDev page. (Votes: AYES: 15 NAYS: 2)

*Why not?*  Sounds good to me.
Yes!  I do a lot of searches.  It'd be nice having a shortcut.

Just think it's unnecessary and a bit cluttered.

By that reasoning, so is Safari/Camino/Firebird's Google search field. You ever use that?

Yes.

* Some sort of warning when two people edit a page simultaneously. (Votes: AYES: 7 NAYS: 1)

*Maybe it could mark a page as 'being edited' when someone clicks Edit for a page, and unmark it when they save (or cancel- maybe add a cancel button?). The 'being edited' status would expire after five minutes or something. Then, it wouldn't let people edit a page already marked as 'being edited'.*

**Other Wikis just use conflict detection and allow the user to recover. It works quite well.**

When you edit, the page gets a sequence number.  If someone else beats you to a change, you get told "sorry, someone else made a change.  Please add your stuff to the new page" *Does this happen **here** or at other Wikis?*

* When a page is being edited, provide a "Preview" button so we can review our changes before committing them. (Votes: AYES: 9 NAYS: 2)

*There's no need, since we have history to revert mistakes. Just preview by saving the page!*

*Sure, but it clutters the history. Providing a Preview button (in addition to Save Changes) doesn't make it any more difficult to edit a page.*

This is a no-brainer if preview is optional.

* Some sort of indication of the **parts** of pages that have changed since the last revision. (Votes: AYES: 9 NAYS: 0)

*That would be really helpful.* **You might want to consider upping the score of "Provide ... a brief summary ... on RecentChanges" below, then. For one, it's not vague :)**

When browsing the history, it might be nice to offer diffs ... perhaps by incorporating Aaron Swartz's HTML Diff into the wiki. Licensing isn't explicitly stated but it looks like he's given it out a few times. I personally like the strikethru variant that NetNewsWire uses. http://www.aaronsw.com/2002/diff/

A quickDiff function similar to that on the c2 wiki would be most welcome.

The Wikipedia history and diff functions are really nice.

I forgot if I voted for this already, but it's worth voting for twice ;) If the edit isn't at the end, I usually don't even bother trying to figure out what it was, which is a shame, really. Who knows what I'm missing?

[**Nov. 10, 2004**] One problem with this that no one has addressed is, especially as there's no "preview" capability before saving changes to a page, people are saving their changes, reading what they've changed and then going back into Edit to correct any mistakes (unless they're perfect; or lazy). By the time anyone else is likely to see the new content the highlighting may only be around some word or letter (or if they've gone back in and deleted a word, around nothing at all). Maybe another means to the same ends is to add something along the lines of a \\%\\%DATE\\%\\% tag that people can use when they're making substantive changes or adding comments?

**Easily solved: just diff between the current version and the last one edited *by a different IP*.**

*Even just highlighting (with a different background color, for example) the most recent edit would be a huge improvement over the current situation.*

* Provide a field to give a brief summary of the change made to a page, for convenience on RecentChanges. (Votes: AYES: 3 NAYS: 0)

My 'Aye' is conditional on the field being optional.

* A style OBJECTIVECCODE or somesuch which properly indents and colorizes objC code.  (Votes: AYES: 8 NAYS: 3)

Probably wishful thinking, but it'd be real nice.

Possibly really slow too. And who's definition of 'properly indented and colorized' would it use?

It'd only be slow generating the HTML, and how slow can it be?

There are a number of free HTML-generating, syntax-coloring perl scripts out there, I don't think it would be very difficult to extend one with C/C++ support to handle Objective-C/C++ as well. In addition, the scripts I tested are not slow, they could colorize the snippets that get put on this site in no time, on the fly of course. This would also have the added benefit of being able to handle more languages than just Objective-C/C++.

It would add some much needed color (even if the color was somewhat ugly :) ).

I find colorized code extremely difficult to read due to vision problems.

I don't think this is really neccessary. The code posted here isn't that long (or it shouldn't be), and if you want to work with a particular piece of code, you're going to paste it into your editor anyway. Hmm.... that gives me an idea - *(Whoops, sorry - this is the Most Popular section. See 'Button to copy CODE sections' below.)*

* More powerful search.  Boolean logic would do (AND, OR, etc.) (Votes: AYES: 5 NAYS: 0)

Just use Google for the search...

***RSS-feed of the RecentChanges** I know this is mentioned above, but there wasn't any specific request for CocoaDev to host an RSS-feed. It couldn't be hard to create a perlhack to parse the RecentChanges page and print it out in RSS, I've done it in ObjC, and it must be way easier in Perl. (Votes AYES: 4 NAYS: 0) - See RecentChangesPerlParsing, also see CocoaDevRSSFeed.

* Post the time last modified to the left of the title so the recent changes page is easier to read.  Nix the seconds while you're at it. (Votes: AYES: 3 NAYS: 0)

***2004.10.02** The short text formatting tags using apostrophes (and angled brackets) are a nice improvement over their longer counterparts but they're not ideal. In particular it's easy to accidentally mismatch the 2 and 3-apostrophe tags and not so easy to spot the mismatches until after you've viewed the saved changes. Can bracket-delimited HTML tags be added? The following set would be nice: 


***Formatting:** [b][/b] and [i][/i]; 
***List Types:** [ol][/ol] and [ul][/ul]; 
***List Items:** [li] and (optionally) [/li]; 
***Code:** [code][/code] (for *in-line* plain text like BEGIN/ENDCODESTYLE) and [pre][/pre] (like BEGIN/ENDCODE) 
***Lines:** [hr]. 


These tags are fairly standard. They're easier to read while making edits. You gain numbered lists (through [ol]), and shorter tags for lists, list items, CODESTYLE and LINE. It would also be nice if you could use brackets around a WordsJammedTogether name to disable linking (e.g., [W**'ordsJammedTogether]). The only "gotcha" this might pose for parsing is in code (e.g., something like <code>i = array[a][b];</code>) but this can be circumvented by ignoring tags between the [pre] and [code] tag pairs. (Votes: AYES: 3 NAYS: 0)

*Would this interfere with ObjC message syntax? A [url][/url] tag pair would also be useful, for things like http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSString.html#//apple_ref/doc/uid/20000154/BCIECHFE*

***Note:** There's a parsing error in the wiki software if you use the double-double apostrophes tag (to turn off auto-linking) either before another tag or inside a tag pair on the same line, like this: W**'ordsJammedTogether or *like this: M**'oreWordsJammedTogether.*

**That's well-known from C2. Just use *six* apostrophes, not four: *W<nowiki/>ordsJammedTogether*.**




----

**Other requests** - add new ones at the bottom



* Image hosting (for images on CocoaDev pages of course). Otherwise the images on pages are likely to disappear. (Votes: AYES: 3 NAYS: 6)

I voted NAYS but I would be interested in the option to host SIT or DMG files for projects.

* When automatically converting URLs into links, don't include parentheses in the linked text. (Votes: AYES: 2 NAYS: 1)

*Some URLs use parentheses, so it needs to be a little smarter. For example, it could only include a trailing close parenthesis as part of the link when an open parenthesis is not present immediately before the 'protocol' part of the URL.*

* Post the script that runs CocoaDev somewhere so we can contribute. Maybe it is already online somewhere? (Votes: AYES: 2 NAYS: 0)

*I'm willing to do popular modifications - plus the script's a little hacky. Just get together a proposal and I'll stick it to Steven. Trust me :)*

* Automatically send a list of CocoaDev pages modified in the past day to the cocoa-dev list. (Votes: AYES: 2 NAYS: 3)

*Every time CocoaDev gets mentioned on the cocoa-dev list some moron comes and attacks the site.*

**How about a new mailing list for these daily summaries?**

*I suggest moaning at your browser provider until they provide the automatic change checking feature that OmniWeb has given for years. This will let you know when RecentChanges is modified.*

phpWiki has a RSS feed on the recent changes page.  http://phpwiki.sourceforge.net/phpwiki/RssInformation

As I recall, there already exists an RSS feed for recent changes from this site.

*where is it?*

There are a few 3rd party web sites that provide this service, if you really want it. See http://www.changenotes.com/ for one (I have no connection with this site, I just found it via google)

I hacked a little module for the FeedMe RSS-reader that displays CocoaDev's RecentChanges along with other RSS-feeds. It would be nicer though, if there was a real RSS-feed so that one did not have to write a special parser.

OK, I was bored so I hacked a CocoaDev to RSS script and put it on my webserver, see CocoaDevRSSFeed.

* The ability to embed images other than jpgs. All PNGs and some GIFs don't appear to embed propertly - they show up as links rather than inline images. Witness the problem in CocoaLogo. (Votes: AYES: 2 NAYS: 0)

The GIF shows up fine here (Safari 1.0) -- FinlayDobbie

You sure? The file "Java.85.GIF" doesn't embed for me (also Safari 1.0, but the browser shouldn't matter since this is a matter of the wiki generating HTML). Case-sensitivity problem, perhaps? Problem parsing the extension? Likewise the two PNG's don't show up either.

Works fine for me (Safari 1.2) but some images don't show up as links or pictures. I think that's just a broken href though... -JediKnil

* A general find-and-replace for text on *all* pages (instead of having to do a search and then change each page by hand). (Votes: AYES: 1 NAYS: 6)

*There would need to be a general site history in addition to each page's, then...*

I can just see it now: find-and-replace: Cocoa -> poop ... yeah, maybe this isn't such a great idea.

**I don't even see the point of this, really. There aren't a lot of cases where you have to do that... the whole G4 -> GeeFour nonsense was a bit silly.**

*This is dangerous...*

*Do not retain history for some pages (CocoaDiscussions, RecentChanges) to free up more space on the server. (Votes: AYES: 1 NAYS: 2)

*We'd then have to disallow manual editing of these pages - they get WikiSpam pretty often, and history is the easiest way to get rid of it. I hope this server isn't that starved for space.*

***An alternate method for entering page links. WordsJammedTogether is getting out of control; Some of the WordsJammedTogether 's are confusing and some are close to rewriting history.** (Vote: AYES:2 NAYS: 1)

*I don't understand what is suggested here. Could you clarify?*

I think he means being able to specify that some random text is a link, as opposed to forcing it to be SomeRandomText. I gave it an Aye... things like LdAp are pretty silly, but search is case insensitive so it's not *that* big a deal.

***Maybe a newly added label for newly added discussions that are displayed in Discussions and Recent Changes.** (Vote: AYES:1 NAYS: 0)

*Show the last editing date AND the creation date, so you know immediately how old a page is, particularly useful for discussions. (Votes: AYES: 2 NAYS: 0)

* A tag, \\%\\%SYNONYM\\%\\%, which would mark the next word as an alternate name for the current page. (This would be especially handy for plurals.) For example, the NSDictionary page might have '\\%\\%SYNONYM\\%\\%NSDictionaries' on it.  (Votes: AYES: 2 NAYS: 0)

Also useful for stuff like ObjectiveCee and ObjectiveC -> ObjC. Now that the latter is valid WordsJammedTogether, we don't really need the old pages.

* UTF-8.  (Votes: AYES: 2 NAYS: 0)

***Button to copy code sections** A button under each BEGIN/ENDCODE block which would copy the enclosed code to the clipboard. Would save a lot of tedious selecting. (Votes: AYES: 1 NAYS: 0)

*I would kill for this wiki to highlight changes at every revision.  When I open a large discussion and it hasn't been modified at the end, there is no way in hell I can every figure out what's been added/deleted/changed.  That makes me sad. *For instance, what else changed with this edit? There was something. Did it matter?* (Votes: AYES: 2 NAYS: 0)

----
Indeed, it makes baby Jesus cry! :-) 
----

***2004.10.02** For discussions like the M**'ostWanted pages, allow for the year (in yyyy format) to be appended to the page name. This serves 2 purposes: it keeps the pages from getting too big (no more than a year's worth of new topics) and it (therefore) makes it easier to tell the approximate time frame of the entries. (Votes: AYES: 1 NAYS: 0)

***2004.12.21** vpwiki API support. So we can use VoodooPad to edit pages. (Votes: AYES: 1 NAYS:0)

***2004.12.21** Better tracking of page metadata on on index pages (like CocoaDiscussions). Showing page created & modified dates and perhaps even allowing sorting would make those pages much more useful and would cut down on using RecentChanges as the sole gateway to the site's content. (Votes: AYES: 2 NAYS:0)

***2004.12.25** Split CocoaDiscussions into actual discussions and RequestsForHelp (or something like that). The requests page would have sections based on the topic (also useful for the discussions), and a marker (or just a change in the one-line description) that indicates whether or not they've been solved (i.e. place a tilde before the description to indicate a solved request). Right now we have no real place to put requests for help, except for the page with general information. CocoaDiscussions should include real discussions like MultipleInheritance, ExtendsIsEvil, and WrappingIsBad, not HowToConvertRTFToNSString. (Votes: AYES: 1 NAYS: 0)

***2004.12.30** A blockquote style tag. When pasting bits of documentation into a discussion, using italics for the quote can mess up the discussion's flow, just pasting it as plain text may not demark the quote enough, and bold is too strong. Something like the code style, but in a proportional font would be nice. (Votes: AYES: 1 NAYS: 0)

