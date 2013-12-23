---
layout: page
title: FontFacesSizesAndOtherVisualAnnoyances
---

Alright, this is perhaps just a peeve, but there's some serious weirdness going on with this site in Safari. The font size for regular text on *this* page is pretty large, whereas the RecentChanges page has tiny text. I never had this problem with OmniWeb so I'm assuming it's a Safari bug, but I don't really know how to describe it in the bug reporter!

"Your browser renders CocoaDev.com inconsistently at best."

In any case, I was wondering if it was possibly due to questionable HTML. Anybody have any thoughts (beyond the general evilness of <FONT> tags, I mean ;) )? -- RobRix

----

Oh yes, and my other peeve: code wrapped in \\%\\%BEGINCODE\\%\\% and \\%\\%ENDCODE\\%\\% blocks *does not wrap*. This is something which (I think) should be corrected as it makes it far more annoying to view discussions which have code examples; you have to constantly be scrolling sideways in order to read people's comments as well ast he code.

If ProjectBuilder can soft-wrap code, so can we! Remember, horizontal scrollbars are the Enemy! -- RobRix

----

I hope this post wasn't because of my SaveTheTrees post. I tried to insert line breaks in my CODE block to help keep the document as narrow as possible, but you can only do this so much before the code starts looking strange. I agree that text should wrap in the \\%\\%BEGINCODE\\%\\% and \\%\\%ENDCODE\\%\\% blocks like in PB. 

--zootbobbalu

----

You may not know it, but you're actually complaining about the <pre> HTML tag. It's needed to prevent code fragments from going spazzo, but it has the side-effect of making all spaces non-breaking. Go figure.

If some knowledgeable HTML whizz can suggest a better string of HTML tags to wrap CODE blocks with, I'll happily pass them on to StevenFrank.

-- KritTer

1. Use the <table> element. Fixed width in percentances. Code framents go inside. Table should have different background color.

2. Make a simple code colorizer/cleaner using PHP? Colorize keywords, adds linebreaks and white space, e.g something that makes the code more readable?

-- UnnamedCoward

The <table> thing might work, and I will look into adding it to the Perl base. As to the code colorizer, it would kinda have to be in Perl since this whole site is hosted by a single Perl script and StevenFrank may not like major changes to that. You could ask him.

-- KritTer

Perl. Oh now i finally understand what does the .pl stand for. I won't ask him.. atleast not now, because I am a coward :)

PS: Regular reader of this site and ashamed, because forced to use Win XP :(

-- UnnamedCoward

So as not to create false hope, I should warn that my life is rather full right now, so investigating <table> may not happen for a while. You can blame RobRix for some of that fullness, and university for the rest :) -- KritTer

----

Not complaining specifically about that, zootbobbalu, it's been on my mind since the April of the year this site started. 2001 or whenever that was. Long ago :)

I kinda guessed it was <pre>. Damn that annoys me. I'll try to figure out how I fixed it on a site I made... Is CSS okay? (It had better be! :D ) -- RobRix

Yeah, you wish. -- KritTer

:'( no CSS? Alas... -- RobRix

----

Looking at the way this seems to render, is \\%\\%BEGINCODE\\%\\% and \\%\\%ENDCODE\\%\\% blocks nothing more than something like <span style="whitespace: pre;"> and </span>?  If so, shouldn't it be something more like      and ?  This would make it a bit more semantic.  However, what does a \\%\\%[BEGIN|END]CODE\\%\\% block look like anyway?:     NSObject obj = [[NSObject alloc] init];

Hrm ... it's creating a <blockquote><pre>    <font size="3">.  That's part of the reason it's so horrible: explicit font size in a code-block.  Why not just something like:       It would become a block element, have monospaced fonts, be the browser's regular size, and not use a blockquote element for something semantically erroneous.  Just a thought.

-- AgtAlpha

Is that "whitespace: normal ..." stuff standard HTML?! Never seen it before. -- KritTer

Anything that's in the style attribute is CSS.  It can easy be pulled into an external stylesheet, something like:
    
code {
  whitespace: normal;
  font-family: monospace;
  font-size: normal;
  display: block;
}

This would then be complemented with something like `<link rel="stylesheet" href="/path/to/style.css" type="text/css" />` hiding around in the <head> element.  None of the style information should be put directly into the (X)HTML element attributes.  Aren't we supposed to be aiming for separation of style and content?  It's just my thought that doing something in a standard way would make it far easier to maintain, as well.

-- AgtAlpha

Thank you AgtAlpha, defender of liberty and clean HTML! Seriously, I can't think of a good reason not to use CSS, particularly in this site. It degrades gracefully. It's ignored by lynx altogether. It's insanely great and... okay, never mind. Point is, it's probably a good idea. -- RobRix

Throw together a quick stylesheet, email it to me with instructions about exactly what to wrap code blocks with, and I'll try to hook it in and send it off to StevenFrank sometime soon. Deal? :) -- KritTer

I definitely think I can do that.  I suppose it might take a little bit of work (and loading up CocoaDev in some other browsers) to test and debug the CSS.  If someone can create a page that demonstrates an example of each type of \\%\\%SPECIALFORMATTING\\%\\% thing, then I can map them to semantic XHTML elements, with an accompanying CSS.  Likewise, this is all done through PHP, right?  If so, it might be even easier down the road to maintain and do site-wide updates.  But linking in a single stylesheet in the <head>, either through <link rel="stylesheet" /> or <style>@import();</style>, would be best ... it's only one line of XHTML to add to the site. :-)

-- AgtAlpha

