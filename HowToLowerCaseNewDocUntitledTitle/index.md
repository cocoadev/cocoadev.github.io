---
layout: page
title: HowToLowerCaseNewDocUntitledTitle
---

There was a long thread on the cocoa list on how to "fix" NSDocument's untitled document name, which it returns (in English) as "Untitled". Now, the HIG say the first letter should be uncapitalized. One person (m) suggested some code to fix the problem in a subclass, and special cased German:

    
- (NSString *)displayName
{
	NSString	*fixedName;
	NSString	*displayName = [super displayName];
	BOOL neverSaved = NO;

	// Determine if this document was never saved.
	// We do this by checking if the document has a file path. This is
	// complicated (slightly) by the fact that NSDocument's fileURL method
	// is new in Tiger, and NSDocument's filePath is deprecated in Tiger.

	neverSaved = [self fileURL] == nil;
	if ([self methodForSelector:@selector(fileURL)])
	 {
		 neverSaved = [self fileURL] == nil;
	}
	else
	{
		neverSaved = [self fileName] == nil;
	}
	if (neverSaved) // NSString
	{
		//    Special case for German.
		//    German is the only language in the world that capitalizes
		//    all nouns, so our strategy of lowercasing would produce the
		//    wrong result.

		NSDictionary* userDefaults = [[NSUserDefaults standardUserDefaults] dictionaryRepresentation];
		NSArray* myLocalizations = [[NSBundle mainBundle] localizations];
		NSArray* preferredLocalizations = [NSBundle preferredLocalizationsFromArray:myLocalizations 
			forPreferences: [userDefaults objectForKey:@"NSLanguages"]];

		NSString* currentLanguage = [preferredLocalizations objectAtIndex:0];

		if (![currentLanguage isEqualTo:@"German"])
		{
			// Lowercase the displayName so that, for example,
			// "Untitled 4" becomes "untitled 4".
			fixedName = [displayName lowercaseString];
		}
	} else {
		fixedName = displayName;
	}

	return fixedName;
}


Now, this seems more complicated than it needs to be. From what I have heard, its only the first character that seems to be the problem (at least in Western languages). So, it seems to me that if we just lowercase the first letter, that we're in better shape - and we can handle German too ("ohne Title").

Thus, I believe the above code does in fact "fix" the problem for most countries, if not all:

    
- (NSString *)displayName
{
	NSString	*fixedName;
	NSString	*displayName = [super displayName];
	
	if ([self fileURL] == nil) // NSString
	{
		fixedName = [displayName substringToIndex:1] lowercaseString]
			stringByAppendingString:[displayName substringFromIndex:1;
	} else {
		fixedName = displayName;
	}
	return fixedName;
}


This is a question that comes up from time to time, and it would be good if we could just close it out and be done with it.

