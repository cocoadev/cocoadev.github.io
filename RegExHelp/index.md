---
layout: page
title: RegExHelp
---



I'm trying to accomplish a fairly complex task using Regular Expressions and the OgreKit framework. http://goo.gl/OeSCu

My goal is to parse out the contents of PHP functions from raw source code for later modification.  My problem is that my expression will only go up to a single } (so nested if statements will throw it off).

To better explain what I am doing, take the following PHP code:

    function formatDate($date, $offset)
{
	if ($date != "")
	{
		$date = strtotime($date);
		$date = strtotime("+".$offset." hour", $date);
	}
	$date = date("g:i A, M jS Y", $date);	
	return $date;
}
	
function doStuff($something)
{
	return $something;
}


The purpose of my expression would be too loop through all matches and return each of those functions separately. However, because of the nested } provlem, it would only scan up to the end of the first if statement (missing the remaining code).

My regular expression is: *function formatDate\\s*([(][^)]*[)])?\\s*[{](([^}]|[\\n\\r])*)[}]*

If any RegEx masters out there could offer some assistance it would be greatly appreciated :).

I realize this is slightly off topic, but it is ultimately part of a Cocoa applications, so please excuse it.  Thanks!

----

It's provably impossible to match nested braces with a single regular expression. You'll need to write a code loop that counts brace openings and closings. Also, don't forget that braces are "disabled" if found within a string.

----

So would the best method then be to regex out the function start points and store the range, then use NSScanner to go through and count the {'s and }'?  Thanks for your response!.

----

No, you can't regex out the **end** of the function, since you have no way of knowing where it may be, you have to scan from the start, unfortunately.

----

Thanks for all the responses, I have it working with a scanner.

----

I'm working on a Regular Expression that should take the line breaks of certain portions of a string and replace them with spaces (for reformatting documents to wrap to window)

Here is an example of the string... the bold areas are what I want to replace spaces with line breaks in. The sectionheaders are predictable... there are six different possible section headers: (SecHed1|SecHed2|SecHed3|SecHed4|SecHed5|SecHed6)... the sections to reformat always conclude with two line breaks.

    Document Title

Prelude
blah, blah
blah...

information

**SecHed2
Reformat this text, reformat away.. I want to be one long, long
line that can be word-wrapped... remove all my line breaks and
replace them with spaces... all your space are belong to us.
**
information
**SecHed6
Reformat this text, reformat away.. I want to be one long, long
line that can be word-wrapped... remove all my line breaks and
replace them with spaces... all your space are belong to us.
**
and so on and so forth

Does anyone have any idea what kind of regular expression I need to match the line breaks inside the sections?

Thanks!

----

Perhaps some variation on:

    /SecHed[\d](.*?)$/m

would work.

----

RegExps are inherently bad at multiline matching. I suggest looking for     SecHeadN and then reading the text block after it, doing a     s/\n/ /m (Perl syntax for replace newline with space) on the block. This is really no task that requires regexps at all, it would probably suffice with a simple search and replace.

I would say five lines of Perl. If I knew more about the problem -- I didn't really understand it from the description above -- I could write them for you.

Most likely it would be something like the following:

    
#!/usr/bin/perl -wp

s/^(.*)$/$1/ if $section;

$section = 1 if /SecHed\d/;
$section = 0 if /^\s*$/;


But that would probably not work out of the box...

-- TheoHultberg/Iconara 

----

*RegExps are inherently bad at multiline matching.*

Really?

*This is really no task that requires regexps at all.*

Well ... yeah ... you could write a linear parser by splitting the string, reading line by line, counting the SecHeads and new lines, and then filter appropriately. But why bother when you can just ask the regex library to do it for you?

*I would say five lines of Perl.*

To borrow a turn of phrase from George Lucas, I see you have not mastered perl regular expressions, as I have. 

Gentlemen, Behold!

    $fileContents =~ s/(SecHed.*?)\n{2}/$1  /gs;

Treating the string $fileContents as a single line (because regex **is** good at multi-line matching), find all string segments beginning with SecHead and continuing by way of a non-greedy expansion until-and-not-including the next two newline characters, and replace those string segments with themselves plus two spaces. Ok ... it's much easier to write perl in one line than it is to explain perl in one line.

For further reading I suggest TheCamelBook (aka ProgrammingPerl) pages 58-74.

Note that tools like sed only use BasicRegularExpressions, which is unfortunate. The command above requires ExtendedRegularExpressions, like found in Perl and other more modern utlities.

-- MikeTrent

Ah, well, I was on track, anyway.

For interest's sake, AGRegex. I've used it and liked it.

-- RobRix

For completeness' sake, OgreKit. I've switched from AGRegex to OgreKit in my projects - it supports a bunch of different regex flavors, seems faster than AGRegex in my informal tests, and provides a find panel that works as a drop-in replacement for the standard Cocoa one. It's what SubEthaEdit 2.0 uses for its regex support. Main drawback - the docs are in Japanese only at present.

-- John C. Warner

*There's always one more way of doing it* (Larry Wall) --TheoHultberg

I made a very simple wrapper for the regex functions already present in libc. It's CSRegex, and it's good for people who only need simple regex functionality without the bother of havig to include external libraries.

-- WAHa

