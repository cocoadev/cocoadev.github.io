---
layout: page
title: SortingHiraganaAndKatakana
---



I am running into an odd problem.  I have used bindings to control a NSTableView consisting of several columns.  In one of these columns, I keep a list of Japanese entries (hiragana and katakana only; no kanji) and in another I keep a list of English entries.  I can sort the English entries without any difficulties, but when I try to sort the Japanese entries, the sort order is not correct.  It is mostly correct (about 4950 entries out of 5000 are ok); yet some entries end up in the wrong spot.

It looks like if I only use hiragana or katakana, the sorting works properly.  Just when I mix the two, does this problem occur.

I have tried compare, localizedCaseInsensitiveCompare, localizedCompare, but it did not solve the problem.

----

For now, I am asking the bindings to sort the entries by unicode value, which gets rid of this erratic sort order that I saw with compare, localizedCaseInsensitiveCompare, and localizedCompare.  Unfortunately, this doesn't recognize the equivalence of, say, hiragana 'a' and katakana 'a'.  So if there are any better solutions, please let me know.

    
@implementation NSString (UnicodeComparison)

- (NSComparisonResult)unicodeCompare:(NSString *)string
{
	return [self compare:string options:NSLiteralSearch];
}

@end


----

FilenamesArentStrings may be helpful here.

----

Indeed!  Thank you, this solved the problem nicely.

