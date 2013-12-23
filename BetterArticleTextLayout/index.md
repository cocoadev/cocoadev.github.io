---
layout: page
title: BetterArticleTextLayout
---

OK ... I've been lurking CocoaDev for years now and I am finally totally fed up with the way we're laying out text. We all know the problem: some piece of code uses some really long nested Objective-C statement which requires lots of horizontal space to lay out properly.

     void *pixmap = [[[[UltraCoolObject alloc] initWithThing:thing foo:foo timeToLive:kFOREVER - kTimeSinceUNIXEpoch] autorelease] pixmapForSize:window contentView] frameSize;

And lo! All of the human-readable article text is pushed off into oblivion! Sure my web browser has a horizontal scroll bar, but -- and I'm speaking for myself here -- I totally hate scrolling horizontally to read text, because I have to go left, right, left, right constantly. 

What really gets my goat is we have the opportunity to fix this in our Wiki engine. I'll leave the finer details to those of you who are total HTML gear-heads. But for example, we could lay out the article text as an invisible table where "article text" is pinned to a specific size (1024 pixels?), but we allow source code to scroll to the left. Or, we could say all text should scroll, not unlike the default behavior of Project Builder and Xcode. 

Either way, I think whatever we do should be geared around making the discussion text as readable as possible, since the ideas conveyed there are more important than any source example...

-- MikeTrent

----

Thoughts?

Way to prove your point, Mike.  This doesn't come up often, but when it does, I agree 100% -- the current layout scheme stinks -- even if you have a large screen (I'm on a 20 incher) I don't want my friggen' web browser window to span those 20 inches.  

----

If we're taking votes, I think code should go into an HTML textarea - that would solve the scrolling problem and you wouldn't get those wierd invisible error-inducing 'stray' characters that Safari sometimes puts in there when you paste the code into XCode and try to build.

----
How about breaking the statement up:

     
void *pixmap = [[[[UltraCoolObject alloc] initWithThing:thing foo:foo 
                             timeToLive:kFOREVER - kTimeSinceUNIXEpoch] autorelease] 
                             pixmapForSize:window contentView] frameSize;


Of course that would evoke arguments about where the breaks should occur, e.g.,  one argument/line, line filled to ~80 characters, etc.
I think some reasonable* line breaks would be preferred to extremely long lines.  Authors face this complication fairly often and most
break the statements at logical point.
----
I also think a lot of the problems could be solved if people would format their code better -- but I agree, it would be nice if the CSS was fixed so that code boxes would simply "bleed" out of the main article box, which should be 100% or something, *not* 1024 pixels!

----

*How about breaking the statement up...*

My point is I'm tired of doing exactly that. At this point we must concede this workaround is lame because it's manual, it requires all posters to CocoaDev to carefully monitor their own content (which we aren't all doing already), and there is no tool support for figuring out how much text to lay out in a given area. 

I.e., on my computer, with my browser, and my font, 1024 pixels might be 80 characters, but on your system it might be 160 characters. So you format your code for 160 characters. And I'm still scrolling to catch up. 

And again -- HTML provides tools for doing this kind of thing "for free"! The page can describe the layout in terms of screen area, and leave all of the layout mechanics up to the browser. We, the content creators, shouldn't have to worry about this ourselves! 

Do you understand what I mean?

-- MikeTrent

----

We need to break

our comments up,

at least on this page

so it isnt a complete

pain in the @$$ to read!

----

The point is, this page illustrates what everyone is tired of.

--Jacob

----

Looks like this very good idea died quietly. I have employed this PageReanimator (TM) technology to bring it
back into the public consciousness.  (cf "He's not completely dead, just MOSTLY dead!" -- Miracle Max, *The Princess Bride* )

----

I've reverse engineered your PageReanimator and would like to use it to re-suggest putting code styles into a TEXTAREA. This would let people scroll through code while still seeing the surrounding (non code) comments.

----

The only one who can do this is StevenFrank or one of his cohorts. Why doesn't someone (not me) e-mail him and find out what can be done?

Or even better, download the code, make it work, and give a patch back.

----

mmmmm....30 inch flat panel.... aaaaahhhhhh..... *blink* *blink* oh yeah a REAL solution for the average guy... ummm that TEXTAREA thing sounds good... It would also make reading the code verrah nice...

----

  I forwarded my message about the behavior of      blocks and question about whether the implementation guidelines for browsers should be changed to the mozilla-layout and camino-dev lists, and this is what I got back.  I don't know enough about CSS to know what exactly this would do, but I thought someone here might..

(from mozilla-layout)
The problem is that it is in a table.  By default tables expand if they are too small.  What you can try is using the overflow css command on the td tag.

overflow: auto;
overflow: scroll;
etc...

Also I got a note another person referring me to this page:

http://wiki.mozilla.org/GeckoDev?ColumnBalancing

----

I had code blocks embedded in both "pre" and "code" html tags.  I removed the "pre" (preformatted) tag, leaving the "code" tag, and now code blocks seem to wrap correctly.  Let me know how this works for you.  I've only tried it on Safari.  -- StevenFrank

----

The fix works in both safari and FireFox for me. It's much better!

*Edit: I take it back! None of the code blocks have line breaks in them anymore! This is worse than page-widening. A simple s/\n/<br>/ replacement should fix it, though.*

----

You're right... Taking off the "pre" tag loses the line breaks and indentation.  I'll have to try something else.  For now it's back the way it was.  -- StevenFrank

OK, trying a new trick, this time with the CSS "overflow" selector.  Any code blocks wider than 640 pixels should get a horizontal scroll bar instead of pushing out the edge of the page.  This works in Safari, I don't know about other browsers.  Let me know if there are bugs.  -- StevenFrank

----

hmm... kinda neat... but the thing is now we're scrolling horizontally still, it's still difficult to read the code... I was pro-textarea because you could have it set say 20 chars high and a certain number of chars wide and it would line wrap so an overflow line could still be read without scrolling, and surrounding comments could be read even if the code stretches for several pages... that stretching would be inside the text area... (which scrolls vertically)... I must say the new code thing is nifty... I like the dotted outline and adjusted font... but it only copes with half the horizontal scrolling issue... (comments are readable, but code is not...) also the scroll bar is kind of inconvenient... see BetterTruncatingStringsInTableView... in the window size I currently have the code needed to be scrolled... but the scroll bar was like 15 pages down.. oh and I normally scroll with arrow keys but this area wouldn't scroll even after having clicked inside the view.... so to read it I had to go to the bottom, scroll, find my place again, read the end of the line, go down, scroll, go up, read, go down.....even not a textarea but some kind of tag that causes it to wrap...

----

How about some kind of "Code:" label in like a 8-9 pt. bold font for the code sections?

----

Another vote for text areas, but it would be annoying if it happened for every code block (even the single-liners). How about, if it's over 15 lines or so, use a text area, otherwise go with something else?

----

A tip for everyone (else) wondering wtf everyone is talking about here, since nothing's changed: Clear your browser cache. It's probably holding on to the old style sheet.

----

The new code style is great, but could you skip the blockquoute-thing and set the width of the code to something a little bit wider, not much, perhaps 100px or so, that would make it even better. The blockquote is not really needed now when the code has a line around it, you can't fail to see that it's code. 
--TheoHultberg/Iconara

----

How about binding the width to the browser window? Like the edit page.

