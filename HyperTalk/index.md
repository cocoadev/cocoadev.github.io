---
layout: page
title: HyperTalk
---



HyperTalk was the scripting language for AppleComputer's HyperCard system. HyperCard was a tool for creating small applications (known as "stacks") which were capable of surprising complexity.

HyperTalk and HyperCard allowed for robust object oriented development, and made it very easy to create small databases (*very* easy).

In some ways, HyperTalk was very similar to ObjectiveC:


* They are both Object Oriented
* They both allow for dynamic typing (although I don't remember anyone ever calling HyperTalk variables dynamically typed)
* They both can be used for RapidApplicationDevelopment


----

That's really pushing it. HyperCard offered ease-of-development Objective-C doesn't even attempt, but it also used and encouraged non-standard user interface, unlike Cocoa, which is the standard. The environments are not at all alike, and the languages are similar only at the highest possible conceptual level.

[I respectfully disagree with the user interface part. The original copy of HyperCard was written by Bill Atkinson, the author of Quickdraw and lots of other parts of what is now known as MacOS. Back when he wrote it, The Macintosh System actually looked the way HyperCard stacks do. The trouble is that in HyperCard's late years, it was never updated to natively support color (if you don't count HyperCard for the Apple IIGS), and thus it wasn't really feasible to make it look like a real Mac application. -- UliKusterer

Uli, I respectfully must submit that your memory is woefully incorrect. What Mac system did you use that ever looked like a HyperCard stack? Ie, one flat plane, without multiple windows, buttons with shadows, et cetra? -- WhoWroteThis

Okay, the shadows weren't quite standard, but HyperCard *did* have multiple windows. It just didn't have native support for scripters to create their own. There were many applications back in '87 who looked a lot like HyperCard. The problem is that HyperCard's GUI failed to evolve with other applications, except for a last attempt with the advent of 2.2. -- UliKusterer]

----

HyperTalk spawned AppleScript. If you look at the two languages, they're pretty similar. AppleScript learned from HyperTalk's mistakes (having a better extension mechanism, for example). AppleScript also *didn't* learn from HyperTalk's mistakes, in that it tried to continue down the "natural language" path, which made it difficult for a lot of people to write HyperTalk code (and AppleScript code).

[Again, I disagree with the assertion of the "natural language part". This is the whole point of HyperTalk. HyperTalk was not intended as a programming language for professional programmers, though that it was used as such shows how powerful and well-designed it really was. The main point of HyperTalk was as a tool of empowerment for "the rest of us" (to paraphrase Apple marketing lingo of the time) and it succeeded at that. -- UliKusterer

Uli, what do you think "natural language" means? Please read the paragraph more carefully. Of course HyperTalk is easier to write than AppleScript, but the AppleScript team didn't intend it to be that way. -- WhoWroteThis

I don't understand what you mean. "natural language" in the case of HyperCard meant English (though there were provisions to support a localized HyperTalk, and I've heard rumors of a French version, though I've never seen it). I simply do not agree with the assertion that it was a mistake to go "down the natural language path" for HyperTalk. For AppleScript it was, but it was actually HyperCard's greatest advantage, just like it was BASIC's before that. The language was written for beginners, who were thus lead into programming much more gently -- UliKusterer]

[AppleScript is indeed similar to HyperTalk, at least in concept. The only problem with AppleScript is that it seemed to change with every major release of the Mac OS, making some previously written scripts suddenly develop weird errors. Also, each application used different commands and different syntax; nothing was standardized except those four or five basic commands (run, open, print, activate, and quit I think they were). -- JonathanBettencourt]

----

HyperTalk was notoriously considered to be a read-only language. Everyone could tell what a given code sequence did, but few people could write a code sequence given a task.

(I dunno... I find that more true for AppleScript than HyperTalk, myself. I still haven't been able to do much that's useful with AS, but I worked day and night in HT for a few years. *Me too, they both read well but I haven't found any reference materials as good as the HyperTalk Reference stack for AS*)

[Again, disagree. Once you understood the basic idea that HyperTalk is more like 'pidgin English', it was ridiculously easy to find out how a command was phrased. For example, a command I simply discovered by trying it (and never read in a manual) was the "there is a" operator, which let you write things like 'if there is a file "jeff" then'. Though, of course, HyperTalk has its flaws. For example, the way support for user-defined menus and multiple windows as it was introduced in HyperCard 2.0 was too programmer-ish, and SuperCard did it much better by making menus and windows full-blown objects like stacks and buttons. -- UliKusterer

Uli, I'm only telling the common tale I've been told by almost everyone I've talked to about HyperCard. I never had trouble with it myself. I created many highly complex stacks in my day. -- WhoWroteThis

I just thought I'd offer a different point of view, as I had a completely different experience, and everyone I've talked to so far, *and* any articles I've read until now, in the press and elsewhere, actually emphasized how readable HyperTalk was. The keyword "self-documenting" was frequently used. It is different for AppleScript, about which I've heard a lot of the complaints you raised. Are you sure the people you were talking to didn't mix up the two languages? They are very similar, after all. I agree the buttons and single-window-ness are definitely HyperCard flaws, but that doesn't really constitute encouragement of non-standard GUI in my book -- UliKusterer]

I started playing with HyperTalk when I was seven. I wrote real scripts (i.e. more complicated than "go to next card", with global and local variables and card handlers that override stack handlers with the same name) before I was ten. Definitely not a read-only language. I'm now 28 and I still have trouble with the AppleScript syntax. ;-) Is there something wrong with me? -- OlivierScherler

You're perfectly normal - there's something wrong with AppleScript ;=)

