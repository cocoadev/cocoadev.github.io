---
layout: page
title: WikiHistoryHighlighting
---

This is a General/CocoaDev engine feature request.... I want highlighting of added lines with a colour that matches the theme that the most recently added text has say 80% saturation, the second most recent has 60%, third recent having 40%  and all older with a normal background...  This could be done with css and a little tweaking of the lookup code to add span tags...

----
Thanks for adding suggestions to General/CocoaDevMostWanted instead.
----
The only change I have seen recently to the engine was brought about by discussion on it's own page ( General/BetterArticleTextLayout ) and this is one I feel very strongly about.  Longer articles get impossible to follow as people edit the top, middle, and end of the page... looking at the whole thing again from top to bottom is a little sub optimal...  I want to see if someone replied to my last comment, or if they saw a fix in a piece of code, etc. It's so frustrating I have thought about making a special browsing program based on General/WebKit that either shows your current General/CocoaDev node in parallel with it's history, or opens both of the stripped of tags into General/FileMerge...

----

the applescript you wanted...

    
display dialog "General/CocoaDev Node?" default answer "General/RecentChanges" buttons {"Cancel", "Ok"} default button "Ok"
if the button returned of the result is "Ok" then
	
	set nodeName to the text returned of the result
	set nodeURL to "http://www.cocoadev.com/index.pl?" & nodeName
	
	set previousURL to previousURLAt(nodeURL)
	
	set oldFile to POSIX path of (((path to temporary items) as string) & "cocoadevOld.txt")
	set newFile to POSIX path of (((path to temporary items) as string) & "cocoadevNew.txt")
	
	set nowText to putNoTagTextAt(nodeURL, newFile)
	set oldText to putNoTagTextAt(previousURL, oldFile)
	
	do shell script "opendiff " & oldFile & " " & newFile
	
end if

on putNoTagTextAt(theURL, fpath)
	do shell script "curl " & theURL & " | perl -pe 's/<.*?>//g;' | tail +10 > " & fpath
end putNoTagTextAt

on previousURLAt(theURL)
	do shell script "curl " & theURL & " | head -n 15 | tail -1 | perl -pe 's/^.*?\\?.*?\\?//;s/\">.*$//' "
	"http://www.cocoadev.com/index.pl?" & the result
end previousURLAt


----
It would definitely be nice to have more hints on the web regarding its history, but I am a little scared at the idea of colored text all over the place, with different saturations,... wouldn't that be very distractful and maybe even ugly? Maybe something less intrusive like indicators on the side of the text would be better. I think any addition to the wiki template should avoid clutter and not interfere with readability. So far, the template has been great.

The best solution is to have a "history" feature wherein the interested reader may compare versions of a page using highlighting to distinguish added text more easily. See wikipedia.
