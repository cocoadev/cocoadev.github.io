---
layout: page
title: DefaultLocalization
---

How do I get the default localization for my computer?  e.g. English, French, Japanese or whatever the user usually wants to see.

Found the answer to my own question.  Straight from the horse's ass... er, mouth.

    
NSUserDefaults *defs = [NSUserDefaults standardUserDefaults];

NSArray *languages = [defs objectForKey:AppleLanguages];

if ([languages containsObject:@"Swedish"]) {

     /* do something here */ 

}


**Well actually, it turns out the *AppleLanguages* isn't a key that you can use.  I searched through the standardUserDefaults, though, and found the following keys -- not sure which of them is most appropriate, or what some of them even are.**

    
NSPreferredSpellServerVendors
NSLanguageName
NSLanguageDomainName


Turns out there isn't one default language that the user has selected, but an ordered (by preference) list of languages the user is willing to handle.  As someone who uses four languages on a daily basis (two of which have a non-roman Roman writing system) I use the input menu a lot, and on every new machine have to poke around the international pref pane, but I never understood that I could list my preference for localization before.  Is this news to anyone here but me?

----
No.  :-) I don't think I understand your question, but the user's preferred language is the one appearing first in AppleLanguages.  The order of preference is set up by the user in the "Language" tab of the "International" pref pane.

----
On another note, is there a way to get the current localization in an app? The "AppleLanguages" key isn't sufficient, because if the user lists, say, Vietnamese first, and my app has no Vietnamese localization, then my interface will be in whatever they listed second (or third, etc.). For now, I just stick a "CurrentLanguage" key in my Localizable.strings, which maps to "en" in English.lproj, "fr" in French.lproj, etc. Is there a better way?

----
Try     [[NSBundle mainBundle] preferredLocalizations].

----
Actually the only problem with the above code is that "AppleLanguages" should be an NSString-- there's no variable with that name.  That is, it should look like this:

    
NSUserDefaults *defs = [NSUserDefaults standardUserDefaults];

NSArray *languages = [defs objectForKey:@"AppleLanguages"];

if ([languages containsObject:@"Swedish"]) {

     /* do something here */ 

}


You can get the preferred language (the top of the user's list) with:

    
NSString *preferredLanguage = [languages objectAtIndex:0];


The result will be a two-letter ISO 3166 style code-- "en" for English, "nl" for Dutch/Nederlands, etc.

