---
layout: page
title: CocoaDevRSSFeed
---

CocoaDev RecentChanges, available as RSS 1.0 and Atom. Hosted by TheoHultberg/Iconara.

http://www.oreillynet.com/images/icons/rss1.gif
http://developer.iconara.net/rss/cocoadev/?type=rss
http://www.oreillynet.com/images/icons/atom.gif
http://developer.iconara.net/rss/cocoadev/?type=atom

If you add "&fulltext=yes" to the URL the content of each item will be the CocoaDev page.

The feeds caches the RecentChanges-page every fifteen minutes. Please don't update more than once every 30 minutes.

----

**The story**

I was bored so I hacked a CocoaDev/RecentChanges to RSS converter and put it on my webserver. It refreshes the feed every ten minutes. It's cached, but my server isn't that fast, so please don't put to much strain on it.

http://developer.iconara.net/rss/cocoadev/

If I get bored again I might put some extra stuff in there (like date and time, currently it's just title and URL). I also have an idea of a feed that let's you have all pages containing a search string AND that are on the recent changes-list (so that I can ask for all pages on the recent changes that contain my name, which most likely means that I'm involved in the discussion).

TheoHultberg/Iconara 


----

**The discussion**

It's a great idea, but it doesn't work for me. When I subscribe to the URL above in NetNewsWire Lite, it never changes the name from "Untitled Source" and it always shows it as empty. If that can be fixed, I think this will be great. Cocoadev is one of the few sites I read regularly that doesn't have an RSS feed. -- MikeAsh

----

    xmllint reports no problem on the feed, and it works fine in NewsFire. I have been working on it again, so you might have tried using it during that time, dunno. Anyway, I checked it with a feed validator (http://feedvalidator.org/check.cgi?url=http://developer.iconara.net/rss/cocoadev/ ) as well, and it's (now) valid RSS 1.0 (before it was 0.91 since I was lazy). If it still does not work I'll have to do some more hacking.

By the way, what do people prefer? RSS (0.91, 1.0, 2.0) or Atom? It's not much of a hassle providing any.
 
TheoHultberg/Iconara

----

How about Atom or .. something else that works in NetNewsWire?  It does look like a valid feed, but Mike's right that it doesn't show up in NNW.

----

Well, I must say that the problem seems to be in NetNewsWire... the feed validates in the feed validators I have found... maybe NetNewsWire doesn't do RSS 1.0? I have to download it to see what's going on. I might make an Atom feed too, anyway.

TheoHultberg/Iconara

----

I downloaded NetNewsWire and tried adding the feed, didn't work. I tried adding slashdot, didn't work. Conclusion: NetNewsWire doesn't work.

Anyway, the feed is available in Atom as well now: http://developer.iconara.net/rss/cocoadev/?type=atom

TheoHultberg/Iconara

*Slashdot works fine for me, using the URL http://slashdot.org/index.rss , as do about a dozen other feeds that I follow. Yours is the first one I've found that breaks NNW.*

Slashdot does not work if one uses the RDF feed http://slashdot.org/index.rdf in NNW, at least it doesn't for me. I really wish that it worked in NNW, but it doesn't, and I can't see the reason. I'm not trying to be arrogant, but the feed *is* valid, and NNW must be to blame. If someone discovers the problem, I'll be happy to correct it, but until then, sorry guys.

TheoHultberg/Iconara

----

I downloaded the beta of NetNewsWire 2.0, and it works with your Atom feed (as well as slashdot's RDF feed). It still doesn't work with your regular feed, I have no idea why, but it doesn't really matter as long as one of them works. The 2.0 beta seems to work fine so far for everything else, so I guess I'm set. -- MikeAsh

*Slashdot's http://slashdot.org/index.rdf works fine for me in NNW Lite, but try as I might I cannot edit your RSS file to an acceptable state. Weird. -- KritTer*

----

For the Omniweb users, it includes a built-in RSS reader. It works fine with this feed.

----

I don't know if it's NNW being weird again or if it's the feed, but it's not showing me changes to pages that were already on RecentChanges. For example, this page is already on RecentChanges. After I make my edit, the RSS feed (or NNW) won't pick it up as being new. Is that something that can be easily fixed? -- MikeAsh

I haven't tried the cocoa dev feed (yet!), but this may be similar to an issue with PulpFiction that I have not found a way around.  It considers a new article to be the same as an old one if they are from the same feed and have the same title (I think).  An article is never marked as new if you've already read it.  I have problems with this on a few sites. --KenFerry

Seems to me that that is not so strange, after all. RSS has been used mostly for blogs and magazines, where an article is published, and then never changed and republished. So if two items have the same title (or id, or whatever) they are considered to be the same. CocoaDev works differently, of course. So a client should also look at the date modified,  not that any does though. -- Theo

*id would be one thing, but determining equality based solely on title is a bug.  Does a blog writer take into account that each entry title "needs" to be unique?*

This could be worked around in the feed, by simply including the last modification time in the title of the page. Is this a reasonable approach?

I was just going to suggest that... good that some one else had the idea too, then it couldn't be that bad, could it? It would be a problem of sorts if the reader saves old entries and show them (which is the default behaviour of NewsFire, for example), the you would have "CocoaDevRSSFeed 22:03" besides "CocoaDevRSSFeed 22:35" and both would be pointing to the exact same page. But if you tell your newsreader to only show entries that are actually in the feed it's no problem. --Theo

----

Done. The title now includes the time, this page currently shows up as "CocoaDevRSSFeed (13:09:42)". If anyone has suggestions on another format, I am all ears (for example removing the seconds... but I can't be bothered just now...).

TheoHultberg/Iconara

*So you know, you could have the same effect by putting the time in the description...*

Would it really? Do you know for a fact that the description has anything to do with it? The time obviously doesn't (since it's included in the Atom-version of the feed, and I assume that the posters above have tried that), I don't see why the description would effect anything (but I might be wrong, and it would be good to know). --Theo

----

I hate to keep doing this, but there is Yet Another Problem with NNW. Maybe I should switch programs... but I *like* NNW otherwise. Anyway, the problem is this: the times don't show up. NNW shows the titles with nothing between the parentheses, like so: "CocoaDevRSSFeed ()". This is, of course, preventing the fix from actually fixing anything in NNW. Can the fix be fixed so that the fix will fix NNW? -- MikeAsh

What is it with NNW? It didn't work for me at all when I downloaded it (and I hate programs that I have to try to understand, so I'm not going to try again), and now this bug, I can see no reason for it... I'll change the parentheses to a comma and a space instead ("CocoaDevRSSFeed, 18:20:39"), if that doesn't work you'll have to take it to the author of NNW instead =) --Theo

----

**Latest update:** feed names are now on the format "CocoaDevRSSFeed, 18:20:39", and the RSS version of the feed has the same date format as the Atom version (which means that it includes time as well). 

--TheoHultber

----

Actually, I think this might be your bug. :-) When I grab the Atom feed and look at the source, the names have no timestamp. The title lines just look like this:     <title type="text/plain">HowToKillAFrozenThread, </title>. -- MikeAsh

Ooops, spelling misstake (did I mention that I hate PHP?). --Theo

*That seemed to fix it. Thanks!*

----
**I hate to ask... but... um... why are you hosting an RSS feed OUTSIDE the cocoadev server?  Why can't cocoadev host a real RSS feed, which would cut back on traffic and remove one degree of refresh separation?**

There's no reason for it, besides CocoaDev not having an RSS feed, and I can't do anything about it. It's on the CocoaDevMostWanted-list, but StevenFrank probably has better things to do with his time =). It seems that I don't. --Theo

That wasn't directed at you:  I think a cocoadev recent changes RSS is GREAT, and kudos to you for doing it.  Still, maybe StevenFrank could hand out some admin privs to anyone willing to install such a feed.

It would probably take 10 minutes to write something in Perl, if I could look at the CocoaDev-code. The current implementation is in PHP and 60% of the code is just print-statements with the static XML. The actual logic is probably not more than seven or eight lines. --Theo

----

The source code for this Wiki is available at AboutWikiSoftwareUsed . I'd look into coding an RSS feed generator for it, if I had any, errr time. -- jgw

----

Works very well (also with OmniWeb), nice work Theo!

--ThomasSempf
----
Help! http://developer.iconara.net/rss/cocoadev/?type=atom is no longer returning the timestamp in the posting, making NetNewsWire break again. I'm lost without my RSS. Please fix! ;-)  -- MikeAsh

I thought we had established that it wasn't necessary so I removed the timestamp, but now it's there again since it seems to be needed. --Theo

I don't know about other readers, but NNW doesn't detect an article as new if it has the same title as something that was already read, so only very old articles that had been updated were showing up. Thanks for fixing it, it's much better now. -- MikeAsh

----
**New:** the last ten edited pages will have their whole contents included. Looks fantastic in NewsFire. How does it work with other readers? --TheoHultberg

It looks pretty damned swank in NetNewsWire 2. I'll have to change my reading habits a bit, but that's not necessarily bad. Nice job. -- MikeAsh

Works very well with NewsFire 0.6, too. - l0ne

The decision as to which pages are shown in full seems to be kind of broken. I'm seeing it happen quite a bit where my RSS reader finds one or two newly edited pages, and one of them doesn't have the full display. Any ideas what's going on there? -- MikeAsh

It's the ten first items in the feed, that's it. As we have already determined that something is wrong with NNW, I guess that's were the problem lies =) When a page becomes the eleventh last edited item it will no longer have HTML contents, so if the reader caches the items (as NewsFire does), a page may appear twice, once with HTML contents and once without (but with different timestams in the title, so they are two separate items, as far as the reader is concerned). Anyway, I blame it all on NNW, until I see proof of the opposite =). --Theo

----
**New:** instead of the first ten items having the whole page included I have created a new option, the regular feeds will work as they first did, with only the page titles, but if you add the parameter "fulltext=yes" to the URL you will get all pages with the full contents. http://www.feedvalidator.org reports that the regular feeds are fine, but the fulltext-feeds have some problems, can't seem to weed them out. NewsFire doesn't have any problems, though, and     xmllint says they're OK XML. If anyone can help me with the problems I am grateful. --Theo

*Is it possible that there are named HTML entities being introduced? RSS--or at least, feedvalidator--tends to prefer them numbered, like     &#039;, instead of named, like     &trade;*

Nope     xmllint would have picked that up, the feed is valid XML, but apparently not a valid RSS or Atom-feed, the HTML is wrapped inside a CDATA-section, it doesn't matter what named entities are there. What the feed validator complains about is that there is a character \x95 in the feed, which it says is not valid (even though the content is UTF-8, which confuses me), and in the Atom-version of the feed there is something about the content-element claiming to be inline, which I don't understan either. You can see the errors yourself at http://feedvalidator.org/check.cgi?url=http%3A%2F%2Fdeveloper.iconara.net%2Frss%2Fcocoadev%2Findex.php%3Ftype%3Datom%26fulltext%3Dyes. What I find strange is that I don't do very much different from what I did it before... it's not that it's very pretty to have 20 HTML pages inside a feed, but it's perfectly valid... --Theo

Oh, well, I fixed one of the problems, I read up on Atom and added mode="escaped" to the content-element. Now it's just the bad character I have to weed out. --Theo

----

Hi Theo - I think the post at http://sourceforge.net/mailarchive/message.php?msg_id=10057660 describes the issue.  The table referred to is at http://www.unicode.org/
Public/MAPPINGS/VENDORS/MICSFT/WINDOWS/CP1252.TXT

Nope, there are no such references in the document... it's the actual byte it's complaining about, no a reference to the character. --Theo

----
Are you positive?  Look again.  It's objecting to x92 and x95 which are unicode "private use two" and "message waiting" characters.  However, in the cp1252 encoding they're "right single quotation mark" and "bullet".  Sounds like precisely the issue to me.  It isn't a "mistake", so you only get warned, but it isn't what you mean to do.

Actually, this is related, but, it looks to me like cocoa-dev produces latin-1 output.  If you want to put it in the feed as utf-8 you'll have to convert it.

But it doesn't seem to matter, I've already tried that. I have tried stating that the feed is ISO-8859-1, no result. I have tried converting the bytes I get from CocoaDev to UTF-8, no result. --Theo

----

Cocoa-dev isn't ISO-8859-1, it appears to be Windows Latin-1, cp1252.  Try converting from that encoding to utf-8.  The characters discussed (x92, x95, now x97) aren't used in ISO-Latin-1.

*Very odd, since the CocoaDev server replies with "Content-Type: text/html; charset=ISO-8859-1"... But I could give it a try anyway. Does anyone know how to do this in PHP?* --Theo

Done. Found a code snipplet that did the job. The server replies with ISO-8859-1, but it I guess that I no translation is performed on that which we type in the edit page. Sorry for not beliving you when you pointed me in the right direction. The feeds are now valid. Also found a bug, the items were not ordered in the right order, but now they are. --Theo

----

A problem with the feed has now been fixed, for a few days it has contained only one item, but now it's 20 again. --Theo

----

Is there any way you could add an option to remove the CocoaDev header / footer and extra styles from the feed?  I like this idea, but I want to be able to read the changes in NNW with my normal CSS style sheet applied...I dont think the actual feed ought to introduce its own style - one of the nice things about RSS is being able to get YOUR new the way YOU like it.

Just a thought anyay.

*Yup, the thought has crossed my mind, I have just not bothered to write the regexp to remove the ccd styling. But sure, maybe in the next version --Theo*

Done, now the CocoaDev LookAndFeel is removed by the cache-script. --Theo

----

**New:** to ease the stress of my server (since there are some that update way too often) I've added some features use the last-modified/if-modified-since HTTP-headers to determine if the contents of the feed actually have to be sent. If the reader sends a if-modified-since header that is after the feed was last modified the server will reply with a HTTP 304 Not Modified and won't send the contents. If you have any problems with this, let me know. --Theo

*I recommend the Cacheability Engine at* http://www.mnot.net/cacheability/ *if you want to get the most out of that. (It doesn't seem to think much of the cacheability so far...)*

Thanks for the link, I implemented some of the suggestions, dunno if it will make any difference, it's not *that* much trafic. --Theo

*Just ran the page past the CE again. Apparently, you've not implemented Last-Modified correctly: "validation returned same object".*

I had used     > instead of     >= when comparing the date, it's fixed now. The CE reports OK. --Theo

----

This feed is putting some strain on my server, please be gentle. Seems the connection cap on the database is reached quite often. Maybe I should register a separate account just for this feed. --Theo

----

**Update:** I rewrote the caching mechanisms to minimise the database connections and it seems to work exactly like before, but if you notice anything please notify me. --Theo

