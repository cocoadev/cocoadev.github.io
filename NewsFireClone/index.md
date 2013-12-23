---
layout: page
title: NewsFireClone
---

*This is an attempt to create a free alternative to NewsFire, with similar effects. If you want more info on the various newsreader apps for our platform, see RSSNewsReaders. Some of that information was originally posted here, but has been moved to that page.*

To get off the ground with RSS parsing, start with this example: http://ranchero.com/cocoa/rss/. Atom feeds may require something else.

Does it really use NSTableViewRollover?

-- No, but very similar to it. It seems to find a mysterious way of overriding firstResponder when the user simply hovers over it. The examples that we've worked on so far don't do that yet, and some of the other things that it does are buggy in Tiger, but not Panther.

AFAIK NewsFire just uses a WebView and the CSS :hover pseudo-class.

I'll add some info to WebView, since it seems to be kind of lean right now.

-- After looking inside the app, it appears that it might be a webview, as suggested, but the nib offers no way to tell, since it all seems to be done programmatically. But making a custom table view would be easier, and would allow us to add a better design for reading the article summaries, as the current one switches the view to a single article, when it should still offer the list in the lower half of that section... to do so would be more Apple-like.

Using embedded f-script you can verify that it is in fact a webview.

Here's an example of a table that does some of the basic highlighting: http://iratescotsman.com/ftp/source/itableview.tgz

I'm also trying to work with better parsing, one that correctly interprets the symbols from XML as the proper characters... so far, the best bet seems to be this: http://librdf.org/raptor/ ... but I still can't find a good Atom parser, since NetNewsWire? can now parse Atom, I consider it worthwhile, even if it isn't all that popular. Also of concern would be whether to try to load podcast audio files directly, or to send the feed to iTunes' podcasts list. Maybe we can use the Atom resources page for something? http://www.atomenabled.org/everyone/atomenabled/index.php?c=7

The Vienna project on sourceforge is cool and open source, but goes in a different direction (more like NetNewsWire?, visually), and is Tiger-only with the latest version. Here's the link: http://www.vienna-rss.org/

----

Does anyone really want to do this? I'll contribute if you do. I think it would be a fun project. Someone set up an svn repository and we'll go to town.
--ZacWhite

----

I'll do it, but see if you can get us somewhere to host the thing. I'm not familiar with how to set up that stuff, or how to do sourceforge. But I do already have quite a bit of stuff, and even a couple icons. -- JasonTerhorst

----

https://opensvn.csie.org/

----

Certain recent edits of this page have removed some information that was not obviously inappropriate or inflammatory or even trivial. Can whoever did it attempt to explain why the excised information was deemed to be expendable?

*I was just trying to clean the page, like a "day one" thing, and the more recent removed comments are just flaming people and are inappropriate and off-topic, so they got dumped as well. Also added important info above that got missed in the copy-over from the other page, so it has more on the webview.*

----

Just to clarify, Vienna is not Tiger only. It runs on Mac OSX 10.3.9 too.

*That's understood, but not easily noticed, since a lot of apps lately that are being created right now are practically ignoring those earlier versions.*

