---
layout: page
title: LocalizationTips
---



Here is a localization tip. Instead of using hard coded strings in your source files, use the NSLocalizedString* macros: 
 
    
NSLocalizedString(key, comment)
NSLocalizedStringFromTable(key, tbl, comment)
NSLocalizedStringFromTableInBundle(key, tbl, bundle, comment)
NSLocalizedStringWithDefaultValue(key, tbl, bundle, value, comment)



***key** is the key for the localizable value
***comment** is a comment explaining the purpose of the key
***tbl** is the name of the table in which to find the key
***bundle** is the name of the bundle in which to find the key
*** value** is the localizable value


Now for the really useful parts of this tip. Use the command line tool genstrings to automatically create an initial correctly formatted strings file with comments, keys and values. When you create your key for the NSLocalizedString*, make the key also be the value then you won't need to alter the value for each key for at least one strings file. http://goo.gl/Cx9sQ

type 'genstrings' in Terminal for help.

-- DaveHenderson (**NOTE:** This also works for the similarly named Core Foundation functions)

----
When you have a string like @"I think this is a long string" somewhere in your source code and you want to localize it using NSLocalizedString, you might not want "I think this is a long string" to be the key in the strings file. What I do is this:
    
    NSLocalizedString(@"LONG_STRING", @"I think this is a long string") 

and then I run genstrings over my source and end up with this in the Localizable.strings file:
    
    /* I think this is a long string */
    "LONG_STRING" = "LONG_STRING";

I actually want "I think this is a long string" to be the value and not "LONG_STRING". That's why I wrote a small Python tool (http://www.springenwerk.com/blog/2007/06/converting-comments-into-values-in.html) that takes care of this and uses the comments as the values.

pyGenstrings coverts such files to look like this:
    
    /* I think this is a long string */
    "LONG_STRING" = "I think this is a long string";

That might come closer to what you want...

-- JohannesFahrenkrug

Had you used NSLocalizedStringWithDefaultValue(), genstrings would have worked for you.

----

Once your app is localized, you may find yourself needing to make a change to a localized nib, and contemplating making the exact same change to a bunch of different localized nibs. Fortunately,     nibtool can take care of a lot of the work for you. In order to do this, you need three nibs. First, you need the original non-localized nib, which I'll call O<nowiki/>riginalNonLoc. Then you need the new non-localized nib (so make your changes on a *copy*, or know where you can get the old one out of your VersionControl system), which I'll call N<nowiki/>ewNonLoc. Finally, you need the old localized nib, which I'll call O<nowiki/>riginalLoc. You also need to know where you're going to put the new localized nib, which I'll call (you guessed it) N<nowiki/>ewLoc.

    nibtool will compare the three nibs you give it, figure out what changes you made between O<nowiki/>riginalNonLoc and N<nowiki/>ewNonLoc, and apply those changes to O<nowiki/>riginalLoc. To do this, run the following command:

    
nibtool -I OriginalLoc.nib -p OriginalNonLoc.nib -w NewLoc.nib -d nonexistent.strings NewNonLoc.nib


Note the     nonexistent.strings file passed in the command. For some reason,     nibtool requires you to specify a .strings file even though it should be optional, and even though it still works fine without it. You don't actually need this file to exist, you just need to pass the     -d flag with the name of a .strings file after it. I don't know why.

If everything went well, you should get a N<nowiki/>ewLoc.nib file with the appropriate changes made.     nibtool doesn't always get it perfectly right, so open the new nib in InterfaceBuilder and make sure everything is ok. It's a fair amount of work, but a lot easier than making all of your changes several times.

----

Here's what I don't understand. Apple explicitly intends for developers to periodically re-run genstrings on source files to produce an up-to-date version of Localizable.strings -- but running genstrings either wipes out the previous version of Localizable.strings, or if run with the -a switch, it fails to do any merging at all and appends duplicate versions of perfectly healthy key-value pairs. 

This is crazy. I can write a tool to do the merging myself, making sure not to overwrite a previously defined key, but I can't believe it's necessary. Do I understand this correctly?

----

A note about merging: There's a very decent app I just discovered on the Mac app store called "Localizable Strings Merge": http://www.delitestudio.com/our-apps/localizable-strings-merge - drag the source files on one side, and any existing .strings files on the other side (if any), and it'll add the new strings in, tagged by a comment.

----

What's the best way to localize a Cocoa application? I am currently  using Apple Glot; it keeps crashing and randomly skipping parts like Localizable.strings or the NIB edits. I cannot get it to consistently translate my application.

I've turned on more detailed logging and I see things like:

*Skipping localization of "xxx" by population logic. on the files that are not being put into the _NewLoc .app.*

----

See chapter 13 of the second edition of Aaron Hillegass's book. Hillegass just says to bring up the info panel on the nib, and to click "Add Locale", to add new language versions.

----

I have a NSDictionary(read from the web in the form of <file>.xml) which includes several strings, e.g. "Welcome", "This version", etc.

If I would like to have localized strings in this NSDictionary and read them out in the language selected as System default.
Should I create an Array inside named English.lproj, French.lproj, etc. or something completely else?

----

Why not have an NSDictionary enclosing localized NSDictionaries?

    mydict objectForKey:@"English"] objectForKey:@"Welcome"]

----

How do I read the current System language setting then - I must have some language base as an entrypoint for my reads in my localized [[NSDictionary? :-)

----

You can use NSUserDefaults:

    
[[NSUserDefaults standardUserDefaults] objectForKey:AppleLanguages];


You could also look at the Apple documentation on localization and a few other places), especially NSLocalizedStringFromTable(key, filename, comment).

----

I have a relatively popular piece of software called AcidSearch. I have done my best to keep the software localizable. There's a .strings file with every single localizable string. When I change it in every release, I am forced to choose between (apparently) two difficult methods of solving the problem:

*I can modify every existing localized nib file and send these new nibs to the localizers. Mistakes are made and propagated.
*I can have the localizer contractor localize the nib from scratch with each new version but they may refuse.

----

(1) man nibtool.  Pay special attention to the sections on incremental localization.

(2) AppleGlot.  Free from Apple, hard to automate, spits up lots of xml without deriving any benefit from it. (This is as opposed to the strings files used with nibtool.)

(3) Check out third party apps like http://www.blue-tec.com/locsuite/.

Have a look at translationWizard http://www.moewes.net/translationWizard

See also mmalc's writeup for StepWise: http://www.stepwise.com/Articles/Technical/2003-04-03.01.html.

Check out AppleTrans!  It's probably only useful for nibs when combined with one of the other options above.  http://appletrans.blogspot.com

----

I knew about AppleGlot, but had heard bad things about it mangling nib files. Somehow I managed to completely overlook nibtool.

----

A Python script comparing .strings files (added and deleted keys):
http://maestric.com/wiki/lib/exe/fetch.php?id=mac%3Acocoa%3Ai18n&cache=cache&media=mac:cocoa:diffstrings.py

A few notes about Cocoa i18n and L10n:
http://maestric.com/wiki/doku.php?id=mac:cocoa:i18n

----
Feb. 25, 2008

The best localization tool I've tried so far is iLocalize. It makes the internationalization of my apps a pleasure (compared to everything else I've used). 

----

I made a post about localization because I was frustrated with the lack of information I found. The document should be fairly accurate and hopefully useful.
http://balthamos.blogspot.com/2008/05/introduction-so-you-are-probably-here_01.html

~Balthamos

----

If you're using XCode 3.0 (3.1?) and cannot find 'nibtool', it's because it now is called 'ibtool'.
/Aderstedt

