---
layout: page
title: CocoaDevEngineNotes
---

In an attempt to get CocoaDev working again, I rewrote the engine somewhat. While I was in there anyway, I got the multiple reverse indexes on eg AppleScript working. Just so people know. I think the rest of the changes were all behind-the-scenes - we can now easily wipe version histories to save space, etc.

All together now. "We hate Perl DBM." Good.

-- KritTer

Does this mean out-of-date pages can be deleted? -- DustinVoss

On the server, yes. You'd have to get StevenFrank to do it. -- KritTer

Good job, clean-cut boy... oh never mind. Thanks :) -- RobRix

A little OT here, but still related to the engine:  Does anyone but me get annoyed by needing to repeatedly type things like \\%\\%BEGINCODESTYLE\\%\\%?  I propose that the engine should also include parsing for shorter pseudo-HTMLish tags like for instance [b] and [/b] to demarkate bold areas, [hr] to make a line, etc.  Another option would be to include some buttons on the edit page that would insert snippets.  Something like this code (lifted and modified from a BBS system for inserting smilies) ought to do it:

    
<script language="javascript">
<!--
function popitin(snippet) {
	document.editform.text.value += snippet;
	document.editform.text.focus();
}
-->
</script>
<a href="javascript:popitin('\\%\\%BEGINBOLD\\%\\%')">insert \\%\\%BEGINBOLD\\%\\%</a><br>
<a href="javascript:popitin('\\%\\%ENDBOLD\\%\\%')">insert \\%\\%ENDBOLD\\%\\%</a><br>
<!-- etc -->


You'd also need to add a <code> name="editform" </code> to the form tag.

This code isn't perfect, it puts the text at the end of the end of the textarea instead of at the insertion point; maybe a DOM expert could help here.

// JackNutting


Yes, please. The %% things are difficult to use and difficult to read in edit mode.

*I happen rather to agree with this - except when one's writing code, when they stick out much more than HTML or BB. But how often does one markup code, really. Post a complete list of replacements for everything we currently have and I'll suggest it to StevenFrank. -- KritTer*

----

Sorry about the late response.  Here's a complete list of alternates:

\\%\\%BEGINLIST\\%\\% or [ul] will open a bulleted list 

\\%\\%ITEM\\%\\% or [li] adds an item to an opened bulleted list 

\\%\\%ENDLIST\\%\\% or [/ul] will end a bulleted list 


\\%\\%BEGINBOLD\\%\\% or [b] begins a bold section of text 

\\%\\%ENDBOLD\\%\\% or [/b] ends a bold section of text 


\\%\\%BEGINITALIC\\%\\% or [i] begins an italicized section of text 

\\%\\%ENDITALIC\\%\\% or [/i] ends a italicized section of text 


\\%\\%BEGINCODE\\%\\% or [pre] begins a code snippet 

\\%\\%ENDCODE\\%\\% or [/pre] ends a code snippet 


\\%\\%BEGINCODESTYLE\\%\\% or [tt] begins monospaced text 

\\%\\%ENDCODESTYLE\\%\\% or [/tt] ends monospaced text 


\\%\\%LINE\\%\\% or [hr] adds a horizontal line to the text.

There are lots of other ways to do this (as seen on various wikis), but I like these because they mirror standard HTML tags that are already familiar to lots of people.

// JackNutting

I agree, these are much better --NirSoffer

Can we not use square brackets? I'd hate to see this interfere w/ our ObjC code. -- MikeTrent

----

Since I wrote the mods before this list was up, I just used standard Wiki. **Bold**, *italic* and     code style. -- KritTer

----

See also ShouldWeSwitchToTwiki

