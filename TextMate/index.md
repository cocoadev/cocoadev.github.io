---
layout: page
title: TextMate
---



http://manual.macromates.com/images/project_window_with_tabs.png

General/TextMate -- http://macromates.com/ -- is written in Cocoa but has a custom text view (instead of using General/NSTextView). It tries to mimic most of General/NSTextView's behavior but the reason for ditching it has to do mainly with the fact that it's a lot more work to make General/NSTextView do what I wanted for General/TextMate than to roll my own.

Some of what General/TextMate brings to a "native" Mac text editor is:

*folding of text blocks,
*recordable macros,
*open files in tabs,
*column selection/typing,
*snippets with dynamic content and tab-able placeholders,
*project management with file outline based on file system structure,
*advanced declarative syntax parsing system which allow for context specific behavior/settings etc.,
*smart-typing, and
*extensive shell integration.


General/TextMate is shareware (39.00 EURO which is about $60.00 US) and there is a 30 days trial.

It's written by General/AllanOdgaard who is regularly visiting this wiki, so feel free to ask any Cocoa/programming related questions about General/TextMate on this page (since I've already received a few by email ;) ).

The license scheme I use in General/TextMate for serial numbers is detailed on my blog: http://sigpipe.macromates.com/2004/using-openssl-for-license-keys/ -- so far I've not seen any fake serials, but I have seen binary cracks and some run-time patches to disable the timeout as suggested on General/CocoaInsecurity. One run-time patch uses something I think was called General/TheAPE, it's a program using General/UnSanity's General/ApplicationEnhancer framework and reads a property list describing which classes/methods should be patched to return what. I was not able to find the actual program, but this allow ��crackers�� to write a patch simply by writing a property list (given that there is a method which return value can be changed to disable registration checks).

Generally though it seems that piracy is a negligible problem on the Mac (at least that's my impression based on my current user base, where many are buying the application when they are convinced that it's for them, and not when the trial expires -- but I've only been in the business for 2 months, so my views may change down the road ;) ).

----

General/TextMate has a wiki too - http://wiki.macromates.com/

----

A tidbit from the old discussion that I think is somewhat important: General/TextMate does not properly support all languages yet (e.g. Japanese support is essentially nonexistent).

Here's a post from the author about the subject: http://one.textdrive.com/pipermail/textmate/2004-December/001836.html

*Does General/TextMate conform to the General/NSTextInput protocol? I gather not, from the above. Supporting it would also allow things like General/TextExtras to work with General/TextMate. I know anything I can do with General/TextExtras, I can probably convert to do natively in General/TextMate, but why should I have to?*

**General/TextExtras add a new category to General/NSTextView, so no, General/NSTextInput conformance is not enough to get the General/TextExtras features.**

*Oh yeah... Well, I still want 'em :p*

you could just change General/TextExtras to categorize General/OakTextView instead.

----

The one thing I miss from General/SubEthaEdit when I use General/TextMate is the functions popup.

This was going to be a list, but I couldn't think of anything else!

OK, I prefer SEE's default General/ObjC coloring - it colors Cocoa classes (wrong to have framework classes in the language syntax definition, but useful) and it also makes memory management keywords (retain, release, autorelease, copy, alloc, mutableCopy) **bold** and red. Obviously, General/TextMate's coloring can be changed to work like SEE's.

----

Hey, looking at General/TextMate.. can you just give a few reasons to use it instead of the default xcode editor?  Does it integrate well?  It looks really nice from what I've tried so far... but could you sell me on why I should use it for all my source editing?  Great work... I'll probably buy it soon.

*it's faster and more customizable than the General/XCode editor, for one. you can also use it for all your editing - HTML, python, config files... things that aren't really suited to General/XCode.*

**And it has many productivity features. Stuff like recordable scratch macros, snippets, column-typing, auto-inserting closing quotes, wrapping selection in brackets/quotes etc. is stuff that keeps down the required typing a lot, the new file chooser lets me jump to whatever file I want in a fraction of a second. Then there is stuff like foldings that, while I rarely use it, is very good for when you want an overview of the source, the bookmarks are also very nice and commands gives rise to yet another kind of automation. General/TextMate is really miles ahead of Xcode when it comes to super-user features.** *General/XCode 2.0 has most of these things. I'm using that until TM gets a functions popup.* -- **I personally (being the author of TM) still miss _a lot_ from Xcode, but of course you should stay with the program that suits you the best, I already have enough users bitching about the function popup, so I'm not searching for more! ;)** *I wasn't bitching about TM (I think the separate scrolling functions thingie that's there now is better than the standard popup) I was more expressing my delight at having just discovered text macros in Xcode 2.0 :) I have a block, I think, at getting too into General/TextMate at this point, (into it enough to miss folding, commands, etc.) since it's still evolving rapidly (viz the bundles situation) and I might have to have to relearn anything I learn in the near future. That's a mess of a sentence! Anyways, as TM matures and settles down, I'm sure I'll be getting into it more.*

(N.B. As of sometime early in 2006, General/TextMate has a customizable function popup.)

*Then there is stuff like foldings that, while I rarely use it, is very good for when you want an overview of the source* <-- not half as good as a functions popup, IMO. The lack of that in General/TextMate is the only thing that keeps me from using it more.

Another reason to use General/TextMate  (or any editor other than the one built into General/XCode) for Cocoa development: General/XCode isn't exactly the most stable application on the planet, and having your code open in a separate process could save your data. Yes, General/XCode just crashed on me (but it doesn't look like I lost anything - phew)

*The latest Objective-C bundle from the repository includes a much enhanced visualization of the Xcode building process:*

http://blog.macromates.com/images/Xcodebuild.png

*"General/OakThemeManager"?? Skins?? Say it ain't so!*

hehe... conceptually a theme is just a collection of one or more style sheets, where one style sheet has the ability to bind settings (including colors) to language elements. E.g.:
    
source comments           { background: #A0A0A0; }
text.html keywords.markup { spellChecking: 0; }
source.objc               { addCompletions: ( retain, release, autorelease ); }

So a theme would be *bright on dark* or *dark on bright* and would wrap for a color style sheet + some basic style sheets that setup color-independent (but context sensitive) stuff. Style sheets are however just called settings, since they are normal settings, but allow a scope to be set for the setting.
-- General/AllanOdgaard

----

mmmm... I'm just revisiting General/TextMate (and this page) after being away from it for awhile, and WOW! This app has really come a long way - most of the rough edges are smoothed over, performance is good, and there is a UI for things that you *could* do by editing config files, but who wants to. Perhaps best of all there's an automated nightly build fetching system that's the best auto update mechanism I've seen. Launch the program, if there's a new build it tells you, downloads it, and when it's done there's an "Install & Relaunch" button. Brilliance. Thank you Allan for this wonderful editor!

*Yep. I use General/TextMate for everything now, except for adding/removing files from a target, since unfortunately Xcode's CLI tools don't expose this functionality yet. It can be done with General/AppleScript, but even that doesn't allow you to not run Xcode at all. Alas.*
