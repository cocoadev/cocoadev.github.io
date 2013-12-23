---
layout: page
title: NSLocalizedString
---

Apple's official documentation is this:

<code>
Returns a localized version of a string.

NSString *NSLocalizedString(NSString *key, NSString *comment)
Discussion
Invokes localizedStringForKey:value:table: on the main bundle and a nil table. In order to be parsed correctly by genstrings, key should not contain any high-ASCII characters.

For more information, see NSBundle.
</code>

I have a question: Is the second parameter, the comment parameter, used for key/value lookups?

If not... then how do you deal with situations where one unique English string may localise to different values? Someone (of slightly questionable credulance), tells me that in some languages, a word with have different forms in different places. He says this:

*In French "Print" is translated as "Impression" or "Imprimer" depending if 
the string is for an "informational" thing (like a label, or title) or for 
a "commanding element" (like a button). *

----
I believe that is what the comment is for:

<code>
NSString *NSLocalizedString(@"Print", @"Button text");
NSString *NSLocalizedString(@"Print", @"Informational Tooltip");
</code>
----

The comment is not used in key/value lookups.  However, the key does not have to be the same as the English text - there can be an English localization as well.

If you need to distinguish between "Impression" and "Imprimer", you could use "Print (noun)" and "Print (verb)" as the keys.

